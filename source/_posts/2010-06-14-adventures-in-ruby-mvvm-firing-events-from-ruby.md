---
title: Adventures in Ruby MVVM – Firing Events from Ruby
date: 2010-06-14 13:34:45
layout: post
category: Software
tags: [MVVM, Ruby, Silverlight, WPF]
permalink: /archives/2010/06/14/adventures-in-ruby-mvvm-firing-events-from-ruby/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/)

### My Experiment:

Can I move over to Ruby as my primary programming language when developing WPF and Silverlight applications?&#160; I have been playing around with what it would mean to use Ruby to write my ViewModels, which would also make it easier to incorporate models using Ruby.

My next few blog posts will revolve around some things I have learned with this experiment.&#160; As described in my disclaimer, I am a bit of a hack when it comes to Ruby.&#160; There are probably better ways to do what I am suggesting.&#160; If so, feel free to call me out.&#160; I won’t mind.&#160; Seriously. 

### INotifyPropertyChanged

So, if I am going to write ViewModels in Ruby, the first thing is to implement **INotifyPropertyChanged**, which is an interface that includes one event.&#160; It is used by WPF and SIlverlight to update the UI when something changes:

```csharp
public interface INotifyPropertyChanged
{
    event PropertyChangedEventHandler PropertyChanged;
}
```

But... how do I define events from Ruby?&#160; It’s not like Ruby has events as a language feature.&#160; As it turns out, in C# for every event (Foo), two methods are created: **add_Foo()** and **remove_Foo()** which are mapped from **Foo += handler** and** Foo –= handler**.&#160; So, to get the **PropertyChanged** event methods implemented in Ruby, I just implement the **add_** and **remove_** methods.&#160; Following is the full implementation of a Ruby class that implements **INotifyPropertyChanged**:

```ruby
class TestClass
    include System::ComponentModel::INotifyPropertyChanged

    def initialize
        @handlers = []
    end

    def add_PropertyChanged(handler)
        @handlers << handler
    end

    def remove_PropertyChanged(handler)
        @handlers.delete(handler)
    end

    def PropertyChanged(sender, args)
        @handlers.each { |h| h.invoke(sender, args) }
    end
  end
```


So, with that, the Ruby code can send an event to .Net code by calling **PropertyChanged()**.&#160; 

### But… Seriously… Lets Metaprogram, Shall We?

The previous code is kind of awkward.&#160; Every time you want to declare an event, you have to write this code?

Of course not.&#160; This is Ruby.&#160; We have metaprogramming!&#160; Lets create some code to generate this code.&#160; I talked with a ruby developer friend of mine, [Toby Tripp](http://twitter.com/tobytripp), and he helped me get to this mixin code that will generate the methods for you:

```ruby
module DotNetEvents
  module ClassMethods

    def declare_event( *events )
      events.each do |event|
        define_handler_methods( event )
      end
    end

    def define_handler_methods( event )
      event_name = event.to_s.gsub(/(?:^|_)(.)/) { $1.upcase }

      class_eval <<-EOS
        def handlers( event_name )
          @event_handlers ||= Hash.new
          @event_handlers[ event_name ] ||= []
        end

        def add_#{event_name}( handler )
          handlers( :#{event_name} ) << handler
        end

        def remove_#{event_name}( handler )
          handlers( :#{event_name} ).delete handler
        end

        def #{event_name}( sender, args )
          handlers( :#{event_name} ).each do |handler|
            handler.invoke sender, args
          end
      end
      EOS
    end
  end

  def self.included(receiver)
    receiver.extend ClassMethods
  end
end
```

Now that we have this **declare_event** method, we can use it to define events.&#160; Just implement the interface, and declare the event:

```ruby
class TestClass
  include DotNetEvents
  include System::ComponentModel::INotifyPropertyChanged

  declare_event :PropertyChanged
end

Note that I have, indeed, tested this with WPF.&#160; The WPF system properly hooks up the event and when the Ruby code fires it, the UI gets updated.

From here, I can build my ViewModel base class in Ruby.&#160; Some things to solve still: making notification of PropertyChanged events easy, commands, data binding, ViewModel hookups, etc.&#160; But, at least, I am on my way :)