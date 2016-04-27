---
title: Adventures in MVVM – Binding Commands to ANY Event
date: 2009-08-27 13:48:00
layout: post
category: Software
tags: [MVVM]
permalink: /archives/2009/09/18/introducing-dynamicwrapper/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) 

One of the biggest points of friction for me when implementing the MVVM pattern happens when I need to bind commands to events.&#160; When using [Prism](http://compositewpf.codeplex.com/), I get the Button.Click command binding out of the box, but every other event needs to be wired up individually.&#160; Doing this requires a LOT of boilerplate code that is very easy to get wrong.&#160; In my [last post, I published some code to alleviate that pain](/archive/2009/08/21/adventures-in-mvvm-ndash-generalized-command-behavior-attachments.aspx).&#160; Still, it requires you to write a new behavior and attachment for every event that you want to bind an event to.

For a while now, I have toyed with the idea of just binding commands to events directly.&#160; I ran into a lot of bumps in the road.&#160; For instance, every event handler has a different event argument type.&#160; This requires all of the handlers to be dynamic.&#160; I also couldn’t create an inline command binding – I will surely want to bind more than one event per control – so I need to create a collection of bindings.&#160; Creating arrays of structures created its own troubles – binding only works with FrameworkElements within the visual tree.&#160; This required me to write my own crude binding within my generic behavior.

What follows is very loosely based off of the [Chinch MVVM framework](http://www.codeproject.com/KB/WPF/Cinch.aspx).&#160; I tested this code in Silverlight and WPF and it works really well!

Assume I have a ViewModel that looks like this:

```csharp
public class MainPageViewModel : INotifyPropertyChanged
{
    ...
    public ICommand MouseLeaveCommand { get; private set; }
    public ICommand MouseEnterCommand { get; private set; }
    public ICommand ClickCommand { get; private set; }
    ...
}
```
I can then bind the commands to events on a control (Button, for instance):

```xml
<Button Content="Click Me">
    <Behaviors:Events.Commands>
        <Behaviors:EventCommandCollection>
            <Behaviors:EventCommand CommandName="MouseEnterCommand" EventName="MouseEnter" />
            <Behaviors:EventCommand CommandName="MouseLeaveCommand" EventName="MouseLeave" />
            <Behaviors:EventCommand CommandName="ClickCommand" EventName="Click" />
        </Behaviors:EventCommandCollection>
    </Behaviors:Events.Commands>
</Button>
```
I no longer need to write any extra code whenever I want to attach commands to my events!&#160; There are a few caveats to this code:

1.  The XAML requires the EventCommandCollection to be declared in the XAML.&#160; I struggled to figure out how to eliminate this but gave up.&#160; Someone smarter than me might be able to tell me what I am doing wrong.
2.  This code does not consider command properties.&#160; Every command assumes a null parameter.&#160; If you need parameters (like data context), then you’ll have to do something differently (either use the old-school mechanism or extend this code to handle some special event types).
3.  You don’t bind directly to the command.&#160; Instead, you declare the name of the command (Notice CommandName is not bound).&#160; The behavior binds for you using a primitive mechanism.

Here is the command behavior that does all the work:

```csharp
public class Events
{
    private static readonly DependencyProperty EventBehaviorsProperty =
        DependencyProperty.RegisterAttached(
        "EventBehaviors",
        typeof(EventBehaviorCollection),
        typeof(Control),
        null);

    private static readonly DependencyProperty InternalDataContextProperty =
        DependencyProperty.RegisterAttached(
        "InternalDataContext",
        typeof(Object),
        typeof(Control),
        new PropertyMetadata(null, DataContextChanged));

    private static void DataContextChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e)
    {
        var target = dependencyObject as Control;
        if (target == null) return;

        foreach (var behavior in GetOrCreateBehavior(target))
            behavior.Bind();
    }

    public static readonly DependencyProperty CommandsProperty =
        DependencyProperty.RegisterAttached(
        "Commands",
        typeof(EventCommandCollection),
        typeof(Events),
        new PropertyMetadata(null, CommandsChanged));

    public static EventCommandCollection GetCommands(DependencyObject dependencyObject)
    {
        return dependencyObject.GetValue(CommandsProperty) as EventCommandCollection;
    }

    public static void SetCommands(DependencyObject dependencyObject, EventCommandCollection eventCommands)
    {
        dependencyObject.SetValue(CommandsProperty, eventCommands);
    }

    private static void CommandsChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e)
    {
        var target = dependencyObject as Control;
        if (target == null) return;

        var behaviors = GetOrCreateBehavior(target);
        foreach (var eventCommand in e.NewValue as EventCommandCollection)
        {
            var behavior = new EventBehavior(target);
            behavior.Bind(eventCommand);
            behaviors.Add(behavior);
        }

    }

    private static EventBehaviorCollection GetOrCreateBehavior(FrameworkElement target)
    {
        var behavior = target.GetValue(EventBehaviorsProperty) as EventBehaviorCollection;
        if (behavior == null)
        {
            behavior = new EventBehaviorCollection();
            target.SetValue(EventBehaviorsProperty, behavior);
            target.SetBinding(InternalDataContextProperty, new Binding());
        }

        return behavior;
    }
}

public class EventCommand
{
    public string CommandName { get; set; }
    public string EventName { get; set; }
}

public class EventCommandCollection : List<EventCommand>
{
}

public class EventBehavior : CommandBehaviorBase<Control>
{
    private EventCommand _bindingInfo;

    public EventBehavior(Control control)
        : base(control)
    {

    }

    public void Bind(EventCommand bindingInfo)
    {
        ValidateBindingInfo(bindingInfo);

        _bindingInfo = bindingInfo;

        Bind();
    }

    private void ValidateBindingInfo(EventCommand bindingInfo)
    {
        if(bindingInfo == null) throw new ArgumentException("bindingInfo");
        if (string.IsNullOrEmpty(bindingInfo.CommandName)) throw new ArgumentException("bindingInfo.CommandName");
        if (string.IsNullOrEmpty(bindingInfo.EventName)) throw new ArgumentException("bindingInfo.EventName");
    }

    public void Bind()
    {
        ValidateBindingInfo(_bindingInfo);
        HookPropertyChanged();
        HookEvent();
        SetCommand();
    }

    public void HookPropertyChanged()
    {
        var dataContext = TargetObject.DataContext as INotifyPropertyChanged;
        if (dataContext == null) return;

        dataContext.PropertyChanged -= DataContextPropertyChanged;
        dataContext.PropertyChanged += DataContextPropertyChanged;
    }

    private void DataContextPropertyChanged(object sender, PropertyChangedEventArgs e)
    {
        if (e.PropertyName == _bindingInfo.CommandName)
            SetCommand();
    }

    private void SetCommand()
    {
        var dataContext = TargetObject.DataContext;
        if (dataContext == null) return;

        var propertyInfo = dataContext.GetType().GetProperty(_bindingInfo.CommandName);
        if (propertyInfo == null) throw new ArgumentException("commandName");

        Command = propertyInfo.GetValue(dataContext, null) as ICommand;
    }

    private void HookEvent()
    {
        var eventInfo = TargetObject.GetType().GetEvent(
            _bindingInfo.EventName, BindingFlags.Public | BindingFlags.Instance);
        if (eventInfo == null) throw new ArgumentException("eventName");

        eventInfo.RemoveEventHandler(TargetObject, GetEventMethod(eventInfo));
        eventInfo.AddEventHandler(TargetObject, GetEventMethod(eventInfo));
    }

    private Delegate _method;
    private Delegate GetEventMethod(EventInfo eventInfo)
    {
        if (eventInfo == null) throw new ArgumentNullException("eventInfo");
        if (eventInfo.EventHandlerType == null) throw new ArgumentException("EventHandlerType is null");

        if (_method == null)
        {
            _method = Delegate.CreateDelegate(
                eventInfo.EventHandlerType, this,
                GetType().GetMethod("OnEventRaised",
                BindingFlags.NonPublic | BindingFlags.Instance));
        }

        return _method;
    }

    private void OnEventRaised(object sender, EventArgs e)
    {
        ExecuteCommand();
    }
}

public class EventBehaviorCollection : List<EventBehavior>
{ }
```