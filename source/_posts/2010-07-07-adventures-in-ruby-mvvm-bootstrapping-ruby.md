---
title: Adventures in Ruby MVVM – Bootstrapping Ruby
date: 2010-07-07 02:44:34
layout: post
category: Software
tags: [MVVM, Ruby]
permalink: /archives/2010/07/07/adventures-in-ruby-mvvm-bootstrapping-ruby/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) 

In this post, I want to discuss how I am loading the ViewModels into the View.&#160; When I write my Views, I like to use the tools I have available to me; Visual Studio and/or Expression Blend.&#160; This means that I want a Visual Studio project and I want to be able to use these tools to create new views quickly.&#160; The XAML files that define my views will still be backed by the obligatory auto-generated C# code, but this is where I want my C# code to end (for these experiments).&#160; I don’t want to write an more C# code than that (with one exception).

My ViewModels, however, will be written in Ruby.&#160; I will use RSpec to specify these ViewModels and I will use an editor other than Visual Studio to edit the code; mostly because [Visual Studio does not have any tooling support for Ruby](http://connect.microsoft.com/VisualStudio/feedback/details/479957/ironruby-integration-in-visual-studio-2010)!&#160; At some point, my View world needs to converge with my ViewModel world.&#160; 

To support this, I have written the only C# code that I intend to write in these experiments.&#160; It is a bootstrapper, if you will, to load Ruby ViewModels.&#160; It looks like this:

```csharp
public class ViewModelLocator : DynamicObject
{
    private readonly ScriptEngine engine;

    public ViewModelLocator()
    {
        engine = IronRuby.Ruby.CreateEngine();
        engine.Runtime.LoadAssembly(typeof(INotifyPropertyChanged).Assembly);
        engine.Runtime.LoadAssembly(typeof(ICommand).Assembly);

        LoadAllViewModels();
    }

    private void LoadAllViewModels()
    {
        Directory
            .EnumerateFiles("ViewModels", "*.rb").ToList()
            .ForEach(file => engine.ExecuteFile(file));
    }

    public override bool TryGetMember(GetMemberBinder binder, out object result)
    {
        if (base.TryGetMember(binder, out result))
            return true;

        result = FindViewModel(binder.Name);

        return result != null;
    }

    private object FindViewModel(string name)
    {
        return engine.Execute(name + ".new");
    }
}
```

This **ViewModelLocator** class exists for one reason: to generate Ruby ViewModels.&#160; It creates a Ruby script engine and pre-loads all the ViewModels when it is constructed.&#160; It then sits around, waiting to create new ViewModels for the View:

```xml
<StackPanel Orientation="Horizontal" DataContext="{Binding PersonViewModel, Source={StaticResource VMLocator}}">
    <TextBlock Text="{Binding first}" Margin="5,0" />
    <TextBlock Text="{Binding last}" Margin="5,0" />
</StackPanel>
```
In the XAML above, I am setting the DataContext of the StackPanel to create a new **PersonViewModel **(defined in Ruby). The binding references a **ViewModelLocator** as its source that was defined in App.xaml:

```xml
<Application.Resources>
    <IronRubyMVVM:ViewModelLocator x:Key="VMLocator" />
</Application.Resources>
```

A dead-simple ViewModel for illustration is defined in Ruby: 

```ruby
require File.dirname(__FILE__)  + "/../RubyVM/ViewModelSupport"

class PersonViewModel
    include ViewModelSupport

    declare_notifiable :first, :last

    def initialize
        @first = "Brian"
        @last = "Genisio"
    end
end
```

You can see all the code in action from the [snapshot of my project from which I wrote this post](http://bitbucket.org/briangenisio/rubyvm/src/72b8541771ba/):&#160; I will be working on this project quite a bit after this post, so if you want to see what I have been working on, the [code is always available.](http://bitbucket.org/briangenisio/rubyvm)