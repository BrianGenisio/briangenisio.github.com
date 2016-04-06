---
title: 'Cross-Training in Silverlight & Flex–MVVM vs Presentation Model'
date: 2010-12-29 03:01:26
layout: post
category: Software
tags: [Flex, MVVM, Presentation Model, Silverlight]
permalink: /archives/2010/12/29/cross-training-in-silverlight-flexmvvm-vs-presentation-model/

---

More [Cross-Training in SIlverlight and Flex](/archives/2010/10/24/cross-training-in-silverlight-vs-flex/)

This is probably my favorite topic in this Silverlight/Flex series.&#160; In the Silverlight world, I have [written extensively about the MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) (Model-View-ViewModel) – a separated presentation pattern for stateful clients (like Rich Internet Applications).&#160; When I got into Flex, I was shocked to find out how easy the pattern was to implement.&#160; Developers in the Microsoft space call it MVVM but it was coined by [Martin Fowler](http://martinfowler.com/) many years previous as [Presentation Model](http://martinfowler.com/eaaDev/PresentationModel.html).&#160; I will use these terms interchangeably but mostly refer to MVVM when explaining the SIlverlight story and Presentation Model when explaining the Flex story.

If you do a Google search today for “[MVVM and Flex](http://www.google.com/search?q=mvvm+and+flex)”, the first post is by somebody who claims that MVVM is not a good fit for Flex.&#160; I couldn’t disagree more.&#160; Out of the box, the Flex framework makes it much easier to implement a Presentation Model than similar MVVM implementations in Silverlight.&#160; That is not to say that there aren’t good third-party libraries that make it easier in Silverlight, but without any help, it is easier to do in Flex.&#160; 

I will show some examples and they should speak for themselves.
  <div style="background-color: #e8f3ff">   

### MVVM in Silverlight

In the MVVM pattern, there are two particular interfaces that are interesting to know:

*   **INotifyPropertyChanged** – an interface that lets ViewModels notify the UI when a property changes*   **ICommand** – an interface that allows the UI to execute code on the ViewModels    

Lets say we have a simple application where the model is excluded because it is not relevant to what I am trying to show.&#160; The behavior is simple: when the user presses a button, read the text in the input field and output “Hello &lt;input&gt;”.&#160; A simple implementation of the view might look like this:
```xml
<UserControl.Resources>
    <local:ViewModel x:Key="ViewModel" />
</UserControl.Resources>
<Border BorderThickness="5" CornerRadius="5" DataContext="{StaticResource ViewModel}"
BorderBrush="Black" Height="Auto" Width="Auto">
    <StackPanel Orientation="Horizontal">
        <TextBox Width="100" Margin="3" Text="{Binding Input, Mode=TwoWay}" />
        <Button Content="Say Hello" Margin="3" Command="{Binding SayHelloCommand}" />
        <TextBlock Margin="3" VerticalAlignment="Bottom" Text="{Binding Output}" />
    </StackPanel>
</Border>
```

The ViewModel to support that logic looks like this:

```csharp
public class ViewModel : INotifyPropertyChanged
{
    public string Input { get; set; }

    private string _output = string.Empty;
    public string Output
    {
        get { return _output; }
        set
        {
            if (_output == value)
                return;

            _output = value;
            RaisePropertyChanged("Output");
        }
    }

    public ICommand SayHelloCommand { get; private set; }

    public ViewModel()
    {
        SayHelloCommand = new DelegateCommand(SayHello);
    }

    private void SayHello()
    {
        Output = "Hello, " + Input;
    }

    protected void RaisePropertyChanged(string propertyName)
    {
        var handlers = PropertyChanged;
        if(handlers != null)
            handlers(this, new PropertyChangedEventArgs(propertyName));
    }

    public event PropertyChangedEventHandler PropertyChanged;
}

public class DelegateCommand : ICommand
{
    private readonly Action action;

    public DelegateCommand(Action action)
    {
        this.action = action;
    }

    public bool CanExecute(object parameter)
    {
        return true;
    }

    public void Execute(object parameter)
    {
        action();
    }

    public event EventHandler CanExecuteChanged;
}
```

This example, admittedly, is a bit hyperbolic.&#160; It includes everything you need to write a ViewModel from scratch.&#160; In reality, the **DelegateCommand** is written once and reused.&#160; In addition, most people will at least abstract the **RaisePropertyChanged** and **PropertyChanged** code by moveing it to a **ViewModelBase** class.&#160; On that note, there are several third-party frameworks for making MVVM more pleasant in Silverlight.&#160; I wrote one of those helpers, called [ViewModelSupport](http://viewmodelsupport.codeplex.com/).&#160; Using the powerful, somewhat magical base class, the previous code can be re-written as:

```csharp
public class ViewModel : ViewModelBase
{
    public string Input
    {
        get { return Get<string>("Input"); }
        set { Set("Input", value); }
    }

    public string Output
    {
        get { return Get<string>("Output"); }
        set { Set("Output", value); }
    }

    public void Execute_SayHelloCommand(object param)
    {
        Output = "Hello, " + Input;
    }
}
```

The latter is certainly better than the former, but my point is that there is a lot of ceremony and plumbing involved in order to get Silverlight applications wedged into the MVVM pattern.&#160; Please don’t misunderstand me: MVVM is usually the best pattern for developing separated Silverlight applications.&#160; I am only suggesting that there is a lot of ceremony.

In addition to my [ViewModelSupport](http://viewmodelsupport.codeplex.com/) package, there are several other libraries out there that help you write implement MVVM in Silverlight including:

*   [MVVM Light Toolkit](http://viewmodelsupport.codeplex.com/) by Laurent Bugnion*   [Caliburn Micro](http://caliburnmicro.codeplex.com/) by Rob Eisenberg*   [NotifyPropertyWeaver](http://code.google.com/p/notifypropertyweaver/) by Simon Cropp
</div>

## Presentation Model in Flex

In Flex, the binding system is very different than the way Silverlight does it.&#160; Notifications are similar to **INotifyPropertyChanged** in Silverlight, but there is no interface specifically for property changes.&#160; Instead, there is just an event that gets fired called “&lt;property&gt;Changed” where &lt;property&gt; is the name of the property that changed.&#160; It is also simplified in that you can decorate your class or individual properties with **[Bindable]** and the Flex compiler writes all of your notification code for you.&#160; As far as executing code in the Presentation Model, there is no **ICommand** like in Silverlight – the view simply calls into the Presentation Model directly.&#160; 

Lets say we have a simple application where the model is excluded because it is not relevant to what I am trying to show.&#160; The behavior is simple: when the user presses a button, read the text in the input field and output “Hello &lt;input&gt;”.&#160; A simple implementation of the view might look like this:

```xml
<fx:Script> 
        <![CDATA[ 
            var pm:PresentationModel = new PresentationModel(); 
        ]]> 
</fx:Script> 

<s:BorderContainer borderWeight="5" cornerRadius="5"           
                   borderColor="black" minHeight="0" width="100%"> 
    <s:HGroup left="3" right="3" top="3" bottom="3"> 
        <s:TextInput width="100" text="@{pm.input}"/> 
        <s:Button label="Say Hello" left="6" click="pm.sayHello()" /> 
        <s:Label left="6" verticalAlign="bottom" height="100%" text="{pm.output}"> 
    </s:HGroup> 
</s:BorderContainer>
```

The Presentation Model then looks like this:

```actionscript
[Bindable]
public class PresentationModel
{
    public var input:String;
    public var output:String;

    public function sayHello():void {            
        output = "Hello, " + input;
    }
}
```

That’s it!&#160; The **[Bindable]** flag tells the **input** and **output** properties to send **inputChanged** and **outputChanged** events automatically.&#160; The **sayHello** function gets called directly from the view without the need to hook up a command.&#160; It is really that easy to implement Presentation Models in Flex.

Much like the Silverlight side, there are frameworks that will help you along the way.&#160; Because the binding system is as strong as it is in Flex (more details in a future post), you don’t need much to implement Presentation Models.&#160; At a very minimum, though, I recommend using an Inversion of Control framework like [flex-ioc](http://code.google.com/p/flex-ioc/), [robotlegs](http://www.robotlegs.org/) or [Swiz](http://swizframework.org/).&#160; 
