---
title: Adventures in MVVM – Binding Commands to ANY Event
id: 77
categories:
  - Uncategorized
date: 2009-08-27 13:48:00
tags:
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fgeekswithblogs.net%2FHouseOfBilz%2Farchive%2F2009%2F08%2F27%2Fadventures-in-mvvm-ndash-binding-commands-to-any-event.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-Binding-Commands-to-ANY-Event) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2009%2f08%2f27%2fadventures-in-mvvm-ndash-binding-commands-to-any-event.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2009%2f08%2f27%2fadventures-in-mvvm-ndash-binding-commands-to-any-event.aspx)

One of the biggest points of friction for me when implementing the MVVM pattern happens when I need to bind commands to events.&#160; When using [Prism](http://compositewpf.codeplex.com/), I get the Button.Click command binding out of the box, but every other event needs to be wired up individually.&#160; Doing this requires a LOT of boilerplate code that is very easy to get wrong.&#160; In my [last post, I published some code to alleviate that pain](http://houseofbilz.com/archive/2009/08/21/adventures-in-mvvm-ndash-generalized-command-behavior-attachments.aspx).&#160; Still, it requires you to write a new behavior and attachment for every event that you want to bind an event to.

For a while now, I have toyed with the idea of just binding commands to events directly.&#160; I ran into a lot of bumps in the road.&#160; For instance, every event handler has a different event argument type.&#160; This requires all of the handlers to be dynamic.&#160; I also couldn’t create an inline command binding – I will surely want to bind more than one event per control – so I need to create a collection of bindings.&#160; Creating arrays of structures created its own troubles – binding only works with FrameworkElements within the visual tree.&#160; This required me to write my own crude binding within my generic behavior.

What follows is very loosely based off of the [Chinch MVVM framework](http://www.codeproject.com/KB/WPF/Cinch.aspx).&#160; I tested this code in Silverlight and WPF and it works really well!

Assume I have a ViewModel that looks like this:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:1f9a3ca3-2468-4f21-a990-2c6f90ac7779" class="wlWriterSmartContent">   <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> MainPageViewModel : INotifyPropertyChanged
{
    ...
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> ICommand MouseLeaveCommand { </span><span style="color: #0000ff">get</span><span style="color: #000000">; </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">set</span><span style="color: #000000">; }
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> ICommand MouseEnterCommand { </span><span style="color: #0000ff">get</span><span style="color: #000000">; </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">set</span><span style="color: #000000">; }
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> ICommand ClickCommand { </span><span style="color: #0000ff">get</span><span style="color: #000000">; </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">set</span><span style="color: #000000">; }
    ...
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

I can then bind the commands to events on a control (Button, for instance):

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:ca0c3822-5216-46ce-ae56-ae34648c9b51" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">&lt;</span><span style="color: #800000">Button </span><span style="color: #ff0000">Content</span><span style="color: #0000ff">=&quot;Click Me&quot;</span><span style="color: #0000ff">&gt;</span><span style="color: #000000">
    </span><span style="color: #0000ff">&lt;</span><span style="color: #800000">Behaviors:Events.Commands</span><span style="color: #0000ff">&gt;</span><span style="color: #000000">
        </span><span style="color: #0000ff">&lt;</span><span style="color: #800000">Behaviors:EventCommandCollection</span><span style="color: #0000ff">&gt;</span><span style="color: #000000">
            </span><span style="color: #0000ff">&lt;</span><span style="color: #800000">Behaviors:EventCommand </span><span style="color: #ff0000">CommandName</span><span style="color: #0000ff">=&quot;MouseEnterCommand&quot;</span><span style="color: #ff0000"> EventName</span><span style="color: #0000ff">=&quot;MouseEnter&quot;</span><span style="color: #ff0000"> </span><span style="color: #0000ff">/&gt;</span><span style="color: #000000">
            </span><span style="color: #0000ff">&lt;</span><span style="color: #800000">Behaviors:EventCommand </span><span style="color: #ff0000">CommandName</span><span style="color: #0000ff">=&quot;MouseLeaveCommand&quot;</span><span style="color: #ff0000"> EventName</span><span style="color: #0000ff">=&quot;MouseLeave&quot;</span><span style="color: #ff0000"> </span><span style="color: #0000ff">/&gt;</span><span style="color: #000000">
            </span><span style="color: #0000ff">&lt;</span><span style="color: #800000">Behaviors:EventCommand </span><span style="color: #ff0000">CommandName</span><span style="color: #0000ff">=&quot;ClickCommand&quot;</span><span style="color: #ff0000"> EventName</span><span style="color: #0000ff">=&quot;Click&quot;</span><span style="color: #ff0000"> </span><span style="color: #0000ff">/&gt;</span><span style="color: #000000">
        </span><span style="color: #0000ff">&lt;/</span><span style="color: #800000">Behaviors:EventCommandCollection</span><span style="color: #0000ff">&gt;</span><span style="color: #000000">
    </span><span style="color: #0000ff">&lt;/</span><span style="color: #800000">Behaviors:Events.Commands</span><span style="color: #0000ff">&gt;</span><span style="color: #000000">
</span><span style="color: #0000ff">&lt;/</span><span style="color: #800000">Button</span><span style="color: #0000ff">&gt;</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

I no longer need to write any extra code whenever I want to attach commands to my events!&#160; There are a few caveats to this code:

1.  The XAML requires the EventCommandCollection to be declared in the XAML.&#160; I struggled to figure out how to eliminate this but gave up.&#160; Someone smarter than me might be able to tell me what I am doing wrong.2.  This code does not consider command properties.&#160; Every command assumes a null parameter.&#160; If you need parameters (like data context), then you’ll have to do something differently (either use the old-school mechanism or extend this code to handle some special event types).3.  You don’t bind directly to the command.&#160; Instead, you declare the name of the command (Notice CommandName is not bound).&#160; The behavior binds for you using a primitive mechanism.

Here is the command behavior that does all the work:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:94c70010-05da-4e1c-83c5-6e54a7aae5dd" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> Events
{
    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">readonly</span><span style="color: #000000"> DependencyProperty EventBehaviorsProperty </span><span style="color: #000000">=</span><span style="color: #000000">
        DependencyProperty.RegisterAttached(
        </span><span style="color: #800000">&quot;</span><span style="color: #800000">EventBehaviors</span><span style="color: #800000">&quot;</span><span style="color: #000000">,
        </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(EventBehaviorCollection),
        </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(Control),
        </span><span style="color: #0000ff">null</span><span style="color: #000000">);

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">readonly</span><span style="color: #000000"> DependencyProperty InternalDataContextProperty </span><span style="color: #000000">=</span><span style="color: #000000">
        DependencyProperty.RegisterAttached(
        </span><span style="color: #800000">&quot;</span><span style="color: #800000">InternalDataContext</span><span style="color: #800000">&quot;</span><span style="color: #000000">,
        </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(Object),
        </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(Control),
        </span><span style="color: #0000ff">new</span><span style="color: #000000"> PropertyMetadata(</span><span style="color: #0000ff">null</span><span style="color: #000000">, DataContextChanged));

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> DataContextChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e)
    {
        var target </span><span style="color: #000000">=</span><span style="color: #000000"> dependencyObject </span><span style="color: #0000ff">as</span><span style="color: #000000"> Control;
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (target </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">return</span><span style="color: #000000">;

        </span><span style="color: #0000ff">foreach</span><span style="color: #000000"> (var behavior </span><span style="color: #0000ff">in</span><span style="color: #000000"> GetOrCreateBehavior(target))
            behavior.Bind();
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">readonly</span><span style="color: #000000"> DependencyProperty CommandsProperty </span><span style="color: #000000">=</span><span style="color: #000000">
        DependencyProperty.RegisterAttached(
        </span><span style="color: #800000">&quot;</span><span style="color: #800000">Commands</span><span style="color: #800000">&quot;</span><span style="color: #000000">,
        </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(EventCommandCollection),
        </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(Events),
        </span><span style="color: #0000ff">new</span><span style="color: #000000"> PropertyMetadata(</span><span style="color: #0000ff">null</span><span style="color: #000000">, CommandsChanged));

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> EventCommandCollection GetCommands(DependencyObject dependencyObject)
    {
        </span><span style="color: #0000ff">return</span><span style="color: #000000"> dependencyObject.GetValue(CommandsProperty) </span><span style="color: #0000ff">as</span><span style="color: #000000"> EventCommandCollection;
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> SetCommands(DependencyObject dependencyObject, EventCommandCollection eventCommands)
    {
        dependencyObject.SetValue(CommandsProperty, eventCommands);
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> CommandsChanged(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e)
    {
        var target </span><span style="color: #000000">=</span><span style="color: #000000"> dependencyObject </span><span style="color: #0000ff">as</span><span style="color: #000000"> Control;
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (target </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">return</span><span style="color: #000000">;

        var behaviors </span><span style="color: #000000">=</span><span style="color: #000000"> GetOrCreateBehavior(target);
        </span><span style="color: #0000ff">foreach</span><span style="color: #000000"> (var eventCommand </span><span style="color: #0000ff">in</span><span style="color: #000000"> e.NewValue </span><span style="color: #0000ff">as</span><span style="color: #000000"> EventCommandCollection)
        {
            var behavior </span><span style="color: #000000">=</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> EventBehavior(target);
            behavior.Bind(eventCommand);
            behaviors.Add(behavior);
        }

    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> EventBehaviorCollection GetOrCreateBehavior(FrameworkElement target)
    {
        var behavior </span><span style="color: #000000">=</span><span style="color: #000000"> target.GetValue(EventBehaviorsProperty) </span><span style="color: #0000ff">as</span><span style="color: #000000"> EventBehaviorCollection;
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (behavior </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
        {
            behavior </span><span style="color: #000000">=</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> EventBehaviorCollection();
            target.SetValue(EventBehaviorsProperty, behavior);
            target.SetBinding(InternalDataContextProperty, </span><span style="color: #0000ff">new</span><span style="color: #000000"> Binding());
        }

        </span><span style="color: #0000ff">return</span><span style="color: #000000"> behavior;
    }
}

</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> EventCommand
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> CommandName { </span><span style="color: #0000ff">get</span><span style="color: #000000">; </span><span style="color: #0000ff">set</span><span style="color: #000000">; }
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> EventName { </span><span style="color: #0000ff">get</span><span style="color: #000000">; </span><span style="color: #0000ff">set</span><span style="color: #000000">; }
}

</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> EventCommandCollection : List</span><span style="color: #000000">&lt;</span><span style="color: #000000">EventCommand</span><span style="color: #000000">&gt;</span><span style="color: #000000">
{
}

</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> EventBehavior : CommandBehaviorBase</span><span style="color: #000000">&lt;</span><span style="color: #000000">Control</span><span style="color: #000000">&gt;</span><span style="color: #000000">
{
    </span><span style="color: #0000ff">private</span><span style="color: #000000"> EventCommand _bindingInfo;

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> EventBehavior(Control control)
        : </span><span style="color: #0000ff">base</span><span style="color: #000000">(control)
    {

    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> Bind(EventCommand bindingInfo)
    {
        ValidateBindingInfo(bindingInfo);

        _bindingInfo </span><span style="color: #000000">=</span><span style="color: #000000"> bindingInfo;

        Bind();
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> ValidateBindingInfo(EventCommand bindingInfo)
    {
        </span><span style="color: #0000ff">if</span><span style="color: #000000">(bindingInfo </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">throw</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> ArgumentException(</span><span style="color: #800000">&quot;</span><span style="color: #800000">bindingInfo</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (</span><span style="color: #0000ff">string</span><span style="color: #000000">.IsNullOrEmpty(bindingInfo.CommandName)) </span><span style="color: #0000ff">throw</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> ArgumentException(</span><span style="color: #800000">&quot;</span><span style="color: #800000">bindingInfo.CommandName</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (</span><span style="color: #0000ff">string</span><span style="color: #000000">.IsNullOrEmpty(bindingInfo.EventName)) </span><span style="color: #0000ff">throw</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> ArgumentException(</span><span style="color: #800000">&quot;</span><span style="color: #800000">bindingInfo.EventName</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> Bind()
    {
        ValidateBindingInfo(_bindingInfo);
        HookPropertyChanged();
        HookEvent();
        SetCommand();
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> HookPropertyChanged()
    {
        var dataContext </span><span style="color: #000000">=</span><span style="color: #000000"> TargetObject.DataContext </span><span style="color: #0000ff">as</span><span style="color: #000000"> INotifyPropertyChanged;
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (dataContext </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">return</span><span style="color: #000000">;

        dataContext.PropertyChanged </span><span style="color: #000000">-=</span><span style="color: #000000"> DataContextPropertyChanged;
        dataContext.PropertyChanged </span><span style="color: #000000">+=</span><span style="color: #000000"> DataContextPropertyChanged;
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> DataContextPropertyChanged(</span><span style="color: #0000ff">object</span><span style="color: #000000"> sender, PropertyChangedEventArgs e)
    {
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (e.PropertyName </span><span style="color: #000000">==</span><span style="color: #000000"> _bindingInfo.CommandName)
            SetCommand();
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> SetCommand()
    {
        var dataContext </span><span style="color: #000000">=</span><span style="color: #000000"> TargetObject.DataContext;
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (dataContext </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">return</span><span style="color: #000000">;

        var propertyInfo </span><span style="color: #000000">=</span><span style="color: #000000"> dataContext.GetType().GetProperty(_bindingInfo.CommandName);
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (propertyInfo </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">throw</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> ArgumentException(</span><span style="color: #800000">&quot;</span><span style="color: #800000">commandName</span><span style="color: #800000">&quot;</span><span style="color: #000000">);

        Command </span><span style="color: #000000">=</span><span style="color: #000000"> propertyInfo.GetValue(dataContext, </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">as</span><span style="color: #000000"> ICommand;
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> HookEvent()
    {
        var eventInfo </span><span style="color: #000000">=</span><span style="color: #000000"> TargetObject.GetType().GetEvent(
            _bindingInfo.EventName, BindingFlags.Public </span><span style="color: #000000">|</span><span style="color: #000000"> BindingFlags.Instance);
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (eventInfo </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">throw</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> ArgumentException(</span><span style="color: #800000">&quot;</span><span style="color: #800000">eventName</span><span style="color: #800000">&quot;</span><span style="color: #000000">);

        eventInfo.RemoveEventHandler(TargetObject, GetEventMethod(eventInfo));
        eventInfo.AddEventHandler(TargetObject, GetEventMethod(eventInfo));
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> Delegate _method;
    </span><span style="color: #0000ff">private</span><span style="color: #000000"> Delegate GetEventMethod(EventInfo eventInfo)
    {
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (eventInfo </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">throw</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> ArgumentNullException(</span><span style="color: #800000">&quot;</span><span style="color: #800000">eventInfo</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (eventInfo.EventHandlerType </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">) </span><span style="color: #0000ff">throw</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> ArgumentException(</span><span style="color: #800000">&quot;</span><span style="color: #800000">EventHandlerType is null</span><span style="color: #800000">&quot;</span><span style="color: #000000">);

        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (_method </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
        {
            _method </span><span style="color: #000000">=</span><span style="color: #000000"> Delegate.CreateDelegate(
                eventInfo.EventHandlerType, </span><span style="color: #0000ff">this</span><span style="color: #000000">,
                GetType().GetMethod(</span><span style="color: #800000">&quot;</span><span style="color: #800000">OnEventRaised</span><span style="color: #800000">&quot;</span><span style="color: #000000">,
                BindingFlags.NonPublic </span><span style="color: #000000">|</span><span style="color: #000000"> BindingFlags.Instance));
        }

        </span><span style="color: #0000ff">return</span><span style="color: #000000"> _method;
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> OnEventRaised(</span><span style="color: #0000ff">object</span><span style="color: #000000"> sender, EventArgs e)
    {
        ExecuteCommand();
    }
}

</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> EventBehaviorCollection : List</span><span style="color: #000000">&lt;</span><span style="color: #000000">EventBehavior</span><span style="color: #000000">&gt;</span><span style="color: #000000">
{ }</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>