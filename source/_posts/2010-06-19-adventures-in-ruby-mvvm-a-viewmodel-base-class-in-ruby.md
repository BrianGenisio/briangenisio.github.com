---
title: Adventures in Ruby MVVM – A ViewModel Base Class in Ruby
date: 2010-06-19 02:52:03
layout: post
category: Software
tags: [MVVM, Ruby, Silverlight, WPF]
permalink: /archives/2010/06/19/adventures-in-ruby-mvvm-a-viewmodel-base-class-in-ruby/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/)

In [my last post](/archives/2010/06/14/adventures-in-ruby-mvvm-firing-events-from-ruby/), I talked about how to fire events from Ruby code such that .Net code can subscribe and receive them.&#160; I showed a simple implementation of INotifyPropertyChanged; the interface that is essential to MVVM development in WPF and Silverlight.

With that out of the way, I am moving forward to start building a simple ViewModel base class in Ruby that will let me declare notifiable properties with as little ceremony as possible.

### Background

Before I get started, I’d like to show what a simple notifiable property in C# looks like.&#160; If you are familiar with MVVM, this code is not new to you:

```csharp
private string first;

public string First
{
    get { return first; }

    set
    {
        first = value;
        OnPropertyChanged("First");
    }
}
```
 

### The Same Thing In Ruby

```ruby
def first
  @first
end

def first=(val)
  @first = val
  on_property_changed :first
end
```


### Generalizing

Already, the Ruby code is more readable (in my opinion).&#160; The cool thing is that we can do better – much, much better.&#160; 

By employing meta programming in the base class, these methods can be generated for us.&#160; The result is a ViewModel class that simply declares the properties as notifiable:

```ruby
class ViewModel < ViewModelBase
  declare_notifiable :first, :last
end
```


That’s it!&#160; Since the base class provides the property generation code, you can just declare that you want to create properties that are notifiable and you are ready to go.&#160; To make sure that the properties notify when they are set (and ultimately get updated in the View via data binding), make sure to call the methods (as opposed to the @fields):

```ruby
def update_values
  self.first = "Brian"
  self.last = "Genisio"
end
```

### ViewModelBase code

Here is the base class that makes it all happen.&#160; For the DotNetEvents mixin code, see my [previous post](/archives/2010/06/14/adventures-in-ruby-mvvm-firing-events-from-ruby/).

```ruby
class ViewModelBase
    include DotNetEvents
    include System::ComponentModel::INotifyPropertyChanged
    declare_event :PropertyChanged

    def self.declare_notifiable( *symbols )
      symbols.each do | symbol |
        define_notifiable_property( symbol )
      end
    end

    def self.define_notifiable_property( property_name )
      class_eval <<-EOS
        def #{property_name}
          @#{property_name}
        end

        def #{property_name}=(val)
          @#{property_name} = val
          on_property_changed :#{property_name} 
        end
      EOS
    end

    def on_property_changed(name)
      PropertyChanged(self, System::ComponentModel::PropertyChangedEventArgs.new(name))
    end
end
```

### Up Next…

Next, I will post about implementing commands in the ViewModel that the View can bind to.&#160; My goal is to get to a completely convention-based approach to commands, much like my C# [ViewModel Support](/archives/2010/05/08/adventures-in-mvvm-my-viewmodel-base/).&#160; Stay tuned.