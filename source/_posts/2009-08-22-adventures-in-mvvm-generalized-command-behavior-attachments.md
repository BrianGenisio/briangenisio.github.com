---
title: Adventures in MVVM – Generalized Command Behavior Attachments
date: 2009-08-22 01:45:00
layout: post
category: Software
tags: [MVVM]
permalink: /archives/2009/08/22/adventures-in-mvvm-generalized-command-behavior-attachments/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/)&#160; 

There are several examples on the web that describe the [“Attached Behavior”](/archive/2009/05/29/adventures-in-mvvm-ndash-commanding-with-list-boxes.aspx) pattern in Silverlight and WPF.&#160; This pattern works really well for binding commands in the ViewModel to controls in the View.&#160; The problem with this is that for every behavior, there is a LOT of boilerplate code that goes along with it.&#160; Because the DepencencyProperties need to be static, they cannot be easily abstracted into a common class. 

If you want to attach a **MouseEnterBehavior **to a control, you need to create two or three static Dependency Properties in the MouseEnter class.&#160; They are MouseEnter.Command, MouseEnter.MouseEnterBehavior and optionally, MouseEnter.CommandParameter.
  
```csharp
public class MouseEnter
{
    private static readonly DependencyProperty BehaviorProperty =
        DependencyProperty.RegisterAttached(
            "MouseEnterBehavior",
            typeof(MouseEnterBehavior),
            typeof(Control),
            null);

    public static readonly DependencyProperty CommandProperty =
        DependencyProperty.RegisterAttached(
            "Command",
            typeof(ICommand),
            typeof(MouseEnter),
            new PropertyMetadata(OnSetCommand));

    public static readonly DependencyProperty CommandParameterProperty =
        DependencyProperty.RegisterAttached(
            "CommandParameter",
            typeof(object),
            typeof(MouseEnter),
            new PropertyMetadata(OnSetParameter));
```

And then the &quot;Change Handlers&quot;

```csharp
        private static void OnSetCommand(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
        {
            var target = dependencyObject as Control;
            if (target == null)
                return;

            GetOrCreateBehavior(target).Command = args.NewValue as ICommand;
        }

        private static void OnSetParameter(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
        {
            var target = dependencyObject as Control;
            if (target != null)
            {
                GetOrCreateBehavior(target).CommandParameter = args.NewValue;
            }
        }

        protected static MouseEnterBehavior GetOrCreateBehavior(Control control)
        {
            var behavior = control.GetValue(BehaviorProperty) as MouseEnterBehavior;
            if (behavior == null)
            {
                behavior = new MouseEnterBehavior(control);
                control.SetValue(BehaviorProperty, behavior);
            }

            return behavior;
        }
```

Since the Dependency Properties are static, Silverlight also requires you to in include Get* and Set* static methods:

```csharp
public static void SetCommand(Control control, ICommand command) { control.SetValue(CommandProperty, command); }
public static ICommand GetCommand(Control control) { return control.GetValue(CommandProperty) as ICommand; }
public static void SetCommandParameter(Control control, object parameter) { control.SetValue(CommandParameterProperty, parameter); }
public static object GetCommandParameter(Control buttonBase) { return buttonBase.GetValue(CommandParameterProperty); }
```

This is a classic case of reuse via “Copy and Paste”.&#160; The problem is that there are several places in this code where you need to change three different types and many strings.&#160; If you don’t invoke the magic incantation properly, nothing works.&#160; It will compile but it won’t work (or you will get an obscure XAML parse error).

I cringe whenever I have to employ copy/paste reuse.&#160; In the cases where it is absolutely necessary (such as this), I believe the risk can be reduced proportionately to the complexity of the modification after you paste.&#160; This is why I came up with an **Attachment** base class to generalize all of this boilerplate code.&#160; The previous code can be reduced to:

```csharp
public class MouseEnter : Attachment<Control, MouseEnterBehavior, MouseEnter>
{
    private static readonly DependencyProperty BehaviorProperty = Behavior();
    public static readonly DependencyProperty CommandProperty = Command(BehaviorProperty);
    public static readonly DependencyProperty CommandParameterProperty = Parameter(BehaviorProperty);

    public static void SetCommand(Control control, ICommand command) { control.SetValue(CommandProperty, command); }
    public static ICommand GetCommand(Control control) { return control.GetValue(CommandProperty) as ICommand; }
    public static void SetCommandParameter(Control control, object parameter) { control.SetValue(CommandParameterProperty, parameter); }
    public static object GetCommandParameter(Control buttonBase) { return buttonBase.GetValue(CommandParameterProperty); }
}
```

The only modifications to this copied code exist on the first line. 

What is the class name?&#160; **MouseEnter** (2 places)

What type of control can the behavior attach to? **Control**

What type of behavior do you want to attach? **MouseEnterBehavior**

In addition to the decreased configuration complexity, the actual code that needs to be copied goes from 58 lines of boilerplate code to 11 lines of boilerplate code.&#160; This is a big win, in my opinion.

In this code, I am using the **CommandBehaviorBase** class from the [Prism](http://compositewpf.codeplex.com/) framework.&#160; It is part of the generic constraints.&#160; If you use something else for your behaviors, replace it as you see fit.&#160; Your own base class for command behaviors would slip in nicely, I am sure.

Here is the **Attachment** base class:

```csharp
public class Attachment<TargetT, BehaviorT, AttachmentT>
        where TargetT : Control
        where BehaviorT : CommandBehaviorBase<TargetT>
    {
        public static DependencyProperty Behavior()
        {
            return DependencyProperty.RegisterAttached(
                typeof(BehaviorT).Name,
                typeof(BehaviorT),
                typeof(TargetT),
                null);
        }

        public static DependencyProperty Command(DependencyProperty behaviorProperty)
        {
            return DependencyProperty.RegisterAttached(
                "Command",
                typeof (ICommand),
                typeof(AttachmentT),
                new PropertyMetadata((target, args) => OnSetCommandCallback(target, args, behaviorProperty)));
        }

        public static DependencyProperty Parameter(DependencyProperty behaviorProperty)
        {
            return DependencyProperty.RegisterAttached(
                "CommandParameter",
                typeof (object),
                typeof (AttachmentT),
                new PropertyMetadata((target, args) => OnSetParameterCallback(target, args, behaviorProperty)));
        }

        protected static void OnSetCommandCallback(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e, DependencyProperty behaviorProperty)
        {
            var target = dependencyObject as TargetT;
            if (target == null)
                return;

            GetOrCreateBehavior(target, behaviorProperty).Command = e.NewValue as ICommand;
        }

        protected static void OnSetParameterCallback(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e, DependencyProperty behaviorProperty)
        {
            var target = dependencyObject as TargetT;
            if (target != null)
            {
                GetOrCreateBehavior(target, behaviorProperty).CommandParameter = e.NewValue;
            }
        }

        protected static BehaviorT GetOrCreateBehavior(Control control, DependencyProperty behaviorProperty)
        {
            var behavior = control.GetValue(behaviorProperty) as BehaviorT;
            if (behavior == null)
            {
                behavior = Activator.CreateInstance(typeof(BehaviorT), control) as BehaviorT;
                control.SetValue(behaviorProperty, behavior);
            }

            return behavior;
        }
    }
```

</div>

And finally, just to complete the example, here is what the **MouseEnterBehavior** looks like:

```csharp
public class MouseEnterBehavior : CommandBehaviorBase<Control>
{
    public MouseEnterBehavior(Control selectableObject)
        : base(selectableObject)
    {
        selectableObject.MouseEnter += (sender, args) => ExecuteCommand();
    }
}
```

And to use it in your XAML, it would look like this:

```xml
<Button Behaviors:MouseEnter.Command="{Binding MouseEnter}" Behaviors:MouseEnter.CommandParameter="Optional Paremeter"/>
```
