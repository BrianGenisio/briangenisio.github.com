---
title: Adventures in MVVM – A Rails-Inspired ViewModel
date: 2009-10-20 13:52:00
layout: post
category: Software
tags: [MVVM, Rails, Ruby]
permalink: /archives/2009/10/20/adventures-in-mvvm-a-rails-inspired-viewmodel/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/)

I have been taking a few weeks off from my personal studies/experiments in the .Net space to learn more about Ruby and Ruby on Rails (RoR).&#160; I have two main goals for this study:

1.  Get to know another platform as an option for future development
2.  Examine how I can incorporate different concepts into my existing framework  

_What follows is intended to document my experimentation with the second bullet:&#160; How might we design a ViewModel if we were in Rails?&#160; My thoughts on this are still forming, and I am still a novice in the RoR arena, so cut me some slack :)._

I am pretty vocal about my thoughts on the MVVM _pattern_.&#160; I really like the pattern a lot and I think it solves a lot of problems that I encounter on a daily basis.&#160; Unfortunately, the implementation of the pattern – ViewModels specifically – often get overly complicated.&#160; This is why there are about a dozen major players in this arena.&#160; The plumbing necessary to create a good ViewModel is tedious and error prone.&#160; A good set of abstractions and tools make the job much easier.

The RoR approach to application development uses a lot of conventions to allow the developer to be as expressive as possible without introducing too much plumbing.&#160; The framework facilitates DRY (Don’t Repeat Yourself) so your controllers declare what they do and the magic under the hood plumbs it all together for you.&#160; How can we translate these ideas to a better implementation of the MVVM pattern?&#160; In this article, I will focus on the ViewModel portion of the MVVM pattern.

### A classic ViewModel implementation

This view is simple.&#160; There is a text box bound to the **Text** property in the ViewModel and a button bound to the **MakeLowercase** command (property) in the ViewModel.&#160; The button is enabled/disabled based on the length of the **Text** property and clicking on the button will cause the text to go to lowercase.

```xml
<TextBox Text="{Binding Text, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}" Grid.Column="0" />
<Button Content="Make Lower" Commands:Click.Command="{Binding MakeLowercase}" Grid.Column="1" />
```

Next is the ViewModel in its pure (no abstractions) form:

```csharp
public class MainPageViewModel : INotifyPropertyChanged
{
    public MainPageViewModel()
    {
        MakeLowercase = new DelegateCommand<pobject>(ExecuteMakeLowercase, CanExecuteMakeLowercase);
    }

    private void ExecuteMakeLowercase(object obj)
    {
        Text = Text.ToLower();
    }

    private bool CanExecuteMakeLowercase(object arg)
    {
        return !string.IsNullOrEmpty(Text);
    }

    private string _text;
    public string Text
    {
        get { return _text; }
        set
        {
            _text = value;
            FirePropertyChanged("Text");
            MakeLowercase.RaiseCanExecuteChanged();
        }
    }

    private DelegateCommand<object> _makeLowercase;
    public DelegateCommand<object> MakeLowercase
    {
        get { return _makeLowercase; }
        set
        {
            _makeLowercase = value;
            FirePropertyChanged("MakeLowercase");
        }
    }

    protected void FirePropertyChanged(string propertyName)
    {
        var handlers = PropertyChanged;
        if (handlers != null)
            handlers(this, new PropertyChangedEventArgs(propertyName));
    }

    public event PropertyChangedEventHandler PropertyChanged;
}
```

### Problems with the traditional ViewModel implementation

1.  Lots of plumbing (Command in the constructor, PropertyChanged in property setters, ExecuteChanged evaluation in the **Text** setter, etc)
2.  Verbose properties (We can’t use auto-properties because of the need to notify of changes
3.  Unrelated events (**Text** property needs to know that **CanExecuteMakeLowercase** needs to be re-evaluated)
4.  INotifyPropertyChanged is error-prone due to string events
5.  Lots of redundancies makes maintenance messy

### What a Rails-Inspired ViewModel might look like

```csharp
public class MainPageViewModel : ActiveViewModel<MainPageViewModel.Properties>
{
    public enum Properties { Text }

    public void Execute_MakeLowercase()
    {
        Set(Properties.Text, Get<string>(Properties.Text).ToLower());
    }

    [DependentUpon(Properties.Text)]
    public bool CanExecute_MakeLowercase()
    {
        return !string.IsNullOrEmpty(Get<string>(Properties.Text));
    }
}
```

My goal was to reduce the amount of plumbing and redundancies in this code to communicate only what it needs to

1.  There is one property named **Text**
2.  The **MakeLowercase** command will act on the **Text** property and make it lowercase
3.  **CanExecuteMakeLowercase** will return true if **Text** has meaningful data
4.  **CanExecuteMakeLowercase** needs to be re-evaluated with **Text** changes

There is a lot of magic in **ActiveViewModel.&#160; **I am using convention to define the properties as a list of enums (**Properties**).&#160; The ActiveViewModel will generate these properties dynamically.&#160; I am also using convention to create commands.&#160; Any method that is prefixed with **Execute_** will be made into a command property.&#160; Any method prefixed with **CanExecute_** will be evaluated in the same command.&#160; Further, the **CanExecute_** method can declare the properties that will cause the commands to re-evaluate, putting the dependency where it belongs – with the method that is dependant upon it!&#160; Testability is really easy: Call the actions and validate the properties (via public Get()).&#160; I didn’t implement it, but actions based on property setters could very easily use the **DependentUpon** attribute to avoid **INotifyPropertyChanged** chaining that can get quite ugly.

### Summary

I think this approach has a lot of potential.&#160; I am posting it in order to move the conversation forward.&#160; When the MVVM dust settles, and we have some best practices flushed out, what do we want our ViewModels to look like?&#160; Even though I consider this class experimental and exploratory (not meant to be used in production), I feel compelled to call out some weaknesses that will (or may) need to be addressed for a more production-ready base class:

1.  The **ActiveViewModel** class will not work in Silverlight.&#160; It relies heavily on custom type descriptors which are not available in Silverlight.&#160; Hopefully, in Silverlight 4.0, we will have some better support for dynamic property generation.&#160; This may be in the form of C# 4.0 (with the DynamicObject.Try[SG]etMember overrides, for instance), property descriptors, or something else (better IronRuby support, perhaps?)
2.  There is not a lot of type safety with the properties.&#160; They are stored internally as plain objects.&#160; The **Get** method lets you get at a type-casted value, but you need to know it when you ask for it.&#160; We might change the property declaration from an enum to a list of name/type pairs and enforce some better type safety in the base class?
3.  Because there are not properties, we loose “equals semantics”.&#160; Get/Set is harder to read/follow than simply assigning and reading properties

Finally, in case you are curious, you can see my implementation (at the time of this post) of the **ActiveViewModel** from my [Launchpad project](http://bazaar.launchpad.net/~briangenisio/mypetproject/Main/annotate/7/ActiveViewModel/ActiveViewModel.cs).