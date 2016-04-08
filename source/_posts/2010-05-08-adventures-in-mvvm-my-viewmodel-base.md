---
title: Adventures in MVVM – My ViewModel Base
date: 2010-05-08 14:02:00
layout: post
category: Software
tags: [MVVM, Silverlight, View Model Support, WPF]
permalink: /archives/2010/05/08/adventures-in-mvvm-my-viewmodel-base/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) 

First, I’d like to say: **THIS IS NOT A NEW MVVM FRAMEWORK.** I tend to believe that MVVM support code should be specific to the system you are building and the developers working on it.&#160; I have yet to find an MVVM framework that does everything I want it to without doing too much.&#160; Don’t get me wrong… there are some good frameworks out there.&#160; I just like to pick and choose things that make sense for me.&#160; As of Silveright 4, they don’t support binding to dynamic properties, so some of the capabilities are lost, but with a [little hacking](/archive/2010/05/14/adventures-in-mvvm-ndash-my-viewmodel-base-ndash-silverlight-support.aspx) we can make it work.

That being said, I want to share my ViewModel base class with the world.&#160; I have had several conversations with people about the problems I have solved using this ViewModel base.&#160; A while back, I posted an article about some experiments with a “[Rails Inspired ViewModel](/archives/2009/10/20/adventures-in-mvvm-a-rails-inspired-viewmodel/)”.&#160; What followed from those ideas was a ViewModel base class that I take with me and use in my projects.&#160; It has a lot of features, all designed to reduce the friction in writing view models. I have put the code out on Codeplex under the project: [ViewModelSupport](http://viewmodelsupport.codeplex.com).

Finally, this article focuses on the ViewModel and only glosses over the View and the Model.&#160; Without all three, you don’t have MVVM.&#160; But this base class is for the ViewModel, so that is what I am focusing on.

**Features:**

1.  [Automatic Command Plumbing](#VMB.1)
2.  [Property Change Notification](#VMB.2)
3.  [Strongly Typed Property Getter/Setters](#VMB.3)
4.  [Dynamic Properties](#VMB.4)
5.  [Default Property values](#VMB.5)
6.  [Derived Properties](#VMB.6)
7.  [Automatic Method Execution](#VMB.7)
8.  [Command CanExecute Change Notification](#VMB.8)
9.  [Design-Time Detection](#VMB.9)
10.  [What about Silverlight?](#VMB.10)  

#### <a name="VMB.1">Automatic Command Plumbing</a>

This feature takes the plumbing out of creating commands.&#160; The common pattern for commands in a ViewModel is to have an Execute method as well as an optional CanExecute method.&#160; To plumb that together, you create an ICommand Property, and set it in the constructor like so:

**Before**
```csharp
public class AutomaticCommandViewModel
{
    public AutomaticCommandViewModel()
    {
        MyCommand = new DelegateCommand(Execute_MyCommand, CanExecute_MyCommand);
    }

    public void Execute_MyCommand()
    {
        // Do something
    }

    public bool CanExecute_MyCommand()
    {
        // Are we in a state to do something?
        return true;
    }

    public DelegateCommand MyCommand { get; private set; }
}
```
With the base class, this plumbing is automatic and the property (MyCommand of type ICommand) is created for you.&#160; The base class uses the convention that methods be prefixed with Execute_ and CanExecute_ in order to be plumbed into commands with the property name after the prefix.&#160; You are left to be expressive with your behavior without the plumbing.&#160; If you are wondering how CanExecuteChanged is raised, see the later section “[Command CanExecute Change Notification](#VMB.8)”.

**After**

```csharp
public class AutomaticCommandViewModel : ViewModelBase
{
    public void Execute_MyCommand()
    {
        // Do something
    }

    public bool CanExecute_MyCommand()
    {
        // Are we in a state to do something?
        return true;
    }
}
```

#### <a name="VMB.2">Property Change Notification</a>

One thing that always kills me when implementing ViewModels is how to make properties that notify when they change (via the INotifyPropertyChanged interface).&#160; There have been many attempts to make this more automatic.&#160; My base class includes one option.&#160; There are others, but I feel like this works best for me.

The common pattern (without my base class) is to create a private backing store for the variable and specify a getter that returns the private field.&#160; The setter will set the private field and fire an event that notifies the change, only if the value has changed.

**Before**

```csharp
public class PropertyHelpersViewModel : INotifyPropertyChanged
{
    private string text;
    public string Text
    {
        get { return text; }
        set
        {
            if(text != value)
            {
                text = value;
                RaisePropertyChanged("Text");
            }
        }
    }

    protected void RaisePropertyChanged(string propertyName)
    {
        var handlers = PropertyChanged;
        if(handlers != null)
            handlers(this, new PropertyChangedEventArgs(propertyName));
    }

    public event PropertyChangedEventHandler PropertyChanged;
}
```

This way of defining properties is error-prone and tedious.&#160; Too much plumbing.&#160; My base class eliminates much of that plumbing with the same functionality:

**After**

```csharp
public class PropertyHelpersViewModel : ViewModelBase
{
    public string Text
    {
        get { return Get<string>("Text"); }
        set { Set("Text", value);}
    }
}
```

#### <a name="VMB.3">Strongly Typed Property Getters/Setters</a>

It turns out that we can do better than that.&#160; We are using a strongly typed language where the use of “Magic Strings” is often frowned upon.&#160; Lets make the names in the getters and setters strongly typed:

**A refinement**

```csharp
public class PropertyHelpersViewModel : ViewModelBase
{
    public string Text
    {
        get { return Get(() => Text); }
        set { Set(() => Text, value); }
    }
}
```

#### <a name="VMB.4">Dynamic Properties</a>

In C# 4.0, we have the ability to program statically OR dynamically.&#160; This base class lets us leverage the powerful dynamic capabilities in our ecosystem. (This is how the automatic commands are implemented, BTW)&#160; By calling Set(“Foo”, 1), you have now created a dynamic property called Foo.&#160; It can be bound against like any static property.&#160; The opportunities are endless.&#160; One great way to exploit this behavior is if you have a customizable view engine with templates that bind to properties defined by the user.&#160; The base class just needs to create the dynamic properties at runtime from information in the model, and the custom template can bind even though the static properties do not exist. All dynamic properties still benefit from the notifiable capabilities that static properties do.

For any nay-sayers out there that don’t like using the dynamic features of C#, just remember this: the act of binding the View to a ViewModel is dynamic already.&#160; Why not exploit it?&#160; Get over it :)

**Just declare the property dynamically**

```csharp
public class DynamicPropertyViewModel : ViewModelBase
{
    public DynamicPropertyViewModel()
    {
        Set("Foo", "Bar");
    }
}
```
**Then reference it normally**

```xml
<TextBlock Text="{Binding Foo}" />
```

#### <a name="VMB.5">Default Property Values</a>

The Get() method also allows for default properties to be set.&#160; Don’t set them in the constructor.&#160; Set them in the property and keep the related code together:

```csharp
public string Text
{
    get { return Get(() => Text, "This is the default value"); }
    set { Set(() => Text, value);}
}
```
#### <a name="VMB.6">Derived Properties</a>

This is something I [blogged about a while back](/archive/2009/11/14/adventures-in-mvvm----dependant-properties-with-inotifypropertychanged.aspx) in more detail.&#160; This feature came from the chaining of property notifications when one property affects the results of another, like this:

**Before**

```csharp
public class DependantPropertiesViewModel : ViewModelBase
{
    public double Score
    {
        get { return Get(() => Score); }
        set
        {
            Set(() => Score, value);
            RaisePropertyChanged("Percentage");
            RaisePropertyChanged("Output");
        }
    }

    public int Percentage
    {
        get { return (int)(100 * Score); }
    }

    public string Output
    {
        get { return "You scored " + Percentage + "%."; }
    }
}
```

The problem is: The setter for Score has to be responsible for notifying the world that Percentage and Output have also changed.&#160; This, to me, is backwards.&#160;&#160;&#160; It certainly violates the “[Single Responsibility Principle](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod).” I have been bitten in the rear more than once by problems created from code like this.&#160; What we really want to do is invert the dependency.&#160; Let the Percentage property declare that it changes when the Score Property changes.

**After**

```csharp
public class DependantPropertiesViewModel : ViewModelBase
{
    public double Score
    {
        get { return Get(() => Score); }
        set { Set(() => Score, value); }
    }

    [DependsUpon("Score")]
    public int Percentage
    {
        get { return (int)(100 * Score); }
    }

    [DependsUpon("Percentage")]
    public string Output
    {
        get { return "You scored " + Percentage + "%."; }
    }
}
```

#### <a name="VMB.7">Automatic Method Execution</a>

This one is extremely similar to the previous, but it deals with method execution as opposed to property.&#160; When you want to execute a method triggered by property changes, let the method declare the dependency instead of the other way around.

**Before**

```csharp
public class DependantMethodsViewModel : ViewModelBase
{
    public double Score
    {
        get { return Get(() => Score); }
        set
        {
            Set(() => Score, value);
            WhenScoreChanges();
        }
    }

    public void WhenScoreChanges()
    {
        // Handle this case
    }
}
```

**After**

```csharp
public class DependantMethodsViewModel : ViewModelBase
{
    public double Score
    {
        get { return Get(() => Score); }
        set { Set(() => Score, value); }
    }

    [DependsUpon("Score")]
    public void WhenScoreChanges()
    {
        // Handle this case
    }
}
```

#### <a name="VMB.8">Command CanExecute Change Notification</a>

Back to Commands.&#160; One of the responsibilities of commands that implement ICommand – it must fire an event declaring that CanExecute() needs to be re-evaluated.&#160; I wanted to wait until we got past a few concepts before explaining this behavior.&#160; You can use the same mechanism here to fire off the change.&#160; In the CanExecute_ method, declare the property that it depends upon.&#160; When that property changes, the command will fire a CanExecuteChanged event, telling the View to re-evaluate the state of the command.&#160; The View will make appropriate adjustments, like disabling the button.

**DependsUpon works on CanExecute methods as well**

```csharp
public class CanExecuteViewModel : ViewModelBase
{
    public void Execute_MakeLower()
    {
        Output = Input.ToLower();
    }

    [DependsUpon("Input")]
    public bool CanExecute_MakeLower()
    {
        return !string.IsNullOrWhiteSpace(Input);
    }

    public string Input
    {
        get { return Get(() => Input); }
        set { Set(() => Input, value);}
    }

    public string Output
    {
        get { return Get(() => Output); }
        set { Set(() => Output, value); }
    }
}
```

#### <a name="VMB.9">Design-Time Detection</a>

If you want to add design-time data to your ViewModel, the base class has a property that lets you ask if you are in the designer.&#160; You can then set some default values that let your designer see what things might look like in runtime.

**Use the IsInDesignMode property**

```csharp
public DependantPropertiesViewModel()
{
    if(IsInDesignMode)
    {
        Score = .5;
    }
}
```

#### <a name="VMB.10">What About Silverlight?</a>

Although you cannot bind directly to dynamic properties and convention-based commands, you CAN bind using a value converter.&#160; This little hack is explained in more detail in my [next post](/archive/2010/05/14/adventures-in-mvvm-ndash-my-viewmodel-base-ndash-silverlight-support.aspx).&#160; Other than that slight difference, all of these features work in Silverlight just as they do in WPF.&#160; You don’t need to code your ViewModels any differently to get it to work, which aids in the sharing of behavior between WPF and Silverlight.

#### Good to go?

So, that concludes the feature explanation of my ViewModel base class.&#160; Feel free to take it, fork it, whatever.&#160; It is [hosted on CodePlex](http://viewmodelsupport.codeplex.com).&#160; When I find other useful additions, I will add them to the public repository.&#160; I use this base class every day.&#160; It is mature, and well tested.&#160; If, however, you find any problems with it, please let me know!&#160; Also, feel free to suggest patches to me via the CodePlex site.&#160; :)