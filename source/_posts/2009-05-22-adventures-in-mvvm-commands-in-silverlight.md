---
title: Adventures in MVVM – Commands in Silverlight
id: 68
categories:
  - Uncategorized
date: 2009-05-22 18:01:00
tags:
---

[**Adventures in MVVM**](http://houseofbilz.com/archive/2009/05/22/adventures-in-mvvm-ndash-model-view-viewmodel.aspx) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2009%2F05%2F22%2Fadventures-in-mvvm-ndash-commands-in-silverlight.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-Commands-in-Silverlight) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f05%2f22%2fadventures-in-mvvm-ndash-commands-in-silverlight.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f05%2f22%2fadventures-in-mvvm-ndash-commands-in-silverlight.aspx) 
  <div style="background-color: #cccccc">   

**<font color="#ff0000">EDIT:</font>**

One of the things that I enjoy most about working with MVVM in Silverlight is how new it is.&#160; When I say “new”, I mean that Silverlight doesn’t support the pattern very well out of the box, so the development community needs to step up and solve these problems.&#160; Some solutions are better than others.&#160; In this article, I solved a problem the best way I knew how.&#160; I urge you to read [Ward Bell’s](http://neverindoubtnet.blogspot.com/) comments to this article, and my responses.&#160; After reflecting on it more, I concede that this is not the best way to implement a lightweight bindable command in SIlverlight.

Instead, I should have just read the Prism 2 source code to see how they implemented commands via attached behaviors.&#160; I would have learned that that I learned attached behaviors wrong in the first place.&#160; In [Julian Dominguez’s blog post on the topic](http://blogs.southworks.net/jdominguez/2008/08/icommand-for-silverlight-with-attached-behaviors/), he walks you through the thought process for attaching commands via behaviors.&#160; Although this is not the final code that made it into Prism, it is very close.&#160; I recommend reading it.

That being said, I will keep my original text in place.&#160; I think that the thought process for how I got there is very useful for learning… at least I find it useful.&#160; Then, be sure to read the comments, visit Julian’s blog and browse the [Prism (CAL)](http://www.microsoft.com/downloads/details.aspx?FamilyID=fa07e1ce-ca3f-4b9b-a21b-e3fa10d013dd&amp;DisplayLang=en) source code.
 </div>  

One of the most important aspects of implementing the MVVM pattern in WPF and SIlverlight is the ability for the UI layer to bind directly to commands in the ViewModel.&#160; The only problem with this:&#160; commands were never implemented in Silverlight.&#160; Even though I (and many others) have ranted about this, our voice has not been heard.&#160; Even with the release of the Silverlight 3 beta, it seems as if we are still pining for commanding in Silverlight.

Many libraries have implemented commands in Silverlight, usually with some sort of static lookup table, mapping buttons to commands.&#160; They include [Prism](http://compositewpf.codeplex.com/), [Caliburn](http://www.codeplex.com/caliburn), [SilverlightFX](http://projects.nikhilk.net/SilverlightFX/) and the [MVVM toolkit](http://msdnrss.thecoderblogs.com/2009/05/03/wpf-mvvm-toolkit-01-released/).&#160; It can feel like overkill to bring in these libraries just to get commanding.&#160; There are plenty of good reasons to use these libraries – don’t get me wrong – but if you are just looking for bindable commands, there is an easier way.

This article will walk you through the process of creating a button with command properties.&#160; This technique can be translated easily to any other control in order to achieve bindable commanding in Silverlight.

### ICommand

The ICommand interface was the only thing that was included from the WPF commanding infrastructure within Silverlight.&#160; The interface is extremely simple:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:1538e2b8-4d98-431a-8cb8-94e4c0fe7f4f" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">interface</span><span style="color: #000000;"> ICommand
{
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Execute(</span><span style="color: #0000FF;">object</span><span style="color: #000000;"> parameter);
    </span><span style="color: #0000FF;">bool</span><span style="color: #000000;"> CanExecute(</span><span style="color: #0000FF;">object</span><span style="color: #000000;"> parameter);
    </span><span style="color: #0000FF;">event</span><span style="color: #000000;"> EventHandler CanExecuteChanged;
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

The requirements of any control that deals with ICommand are:

1.  Call Execute() when a trigger is hit2.  Only call Execute() if CanExecute() returns true3.  Allow a bindable parameter to be passed into Execute() and CanExecute()4.  Disable the control when CanExecute() is false5.  Refresh the enable/disable state of the control when the CanExecuteChanged event is raised

### 

### Implementing CommandButton

Lets start with requirements 1 and 2:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:0f2dbc20-6d51-4802-a63f-1b9fc225713f" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> CommandButton : Button
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> CommandButton()
    {
        Click </span><span style="color: #000000;">+=</span><span style="color: #000000;"> (sender, e) </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;">
        {
            </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (Command </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;"> </span><span style="color: #000000;">&amp;&amp;</span><span style="color: #000000;"> Command.CanExecute(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">))
                Command.Execute(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">);
        };
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> DependencyProperty CommandProperty </span><span style="color: #000000;">=</span><span style="color: #000000;">
        DependencyProperty.Register(</span><span style="color: #800000;">"</span><span style="color: #800000;">Command</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                                    </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(ICommand), </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(CommandButton),
                                    </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> PropertyMetadata(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">));

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ICommand Command
    {
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> GetValue(CommandProperty) </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> ICommand; }
        </span><span style="color: #0000FF;">set</span><span style="color: #000000;"> { SetValue(CommandProperty, value); }
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

With this, you can bind a command in the ViewModel to the view:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:b672d696-ee9e-4357-a2f0-78e492dacb92" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> ViewModel : INotifyPropertyChanged
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ViewModel()
    {
        MyCommand </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> DelegateCommand</span><span style="color: #000000;">&lt;</span><span style="color: #0000FF;">object</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(DoSomething);
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DoSomething(</span><span style="color: #0000FF;">object</span><span style="color: #000000;"> obj)
    {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Do what you want</span><span style="color: #008000;">
</span><span style="color: #000000;">    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ICommand MyCommand { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }

   </span><span style="color: #008000;">//</span><span style="color: #008000;"> The rest of your ViewModel</span><span style="color: #008000;">
</span><span style="color: #000000;">}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

This XAML creates a CommandButton in place of a Button:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:5434a91a-0a34-44d3-8c13-2fec0744c14e" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">local:CommandButton </span><span style="color: #FF0000;">Content</span><span style="color: #0000FF;">="Click Me"</span><span style="color: #FF0000;"> Command</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding MyCommand}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### Adding Parameters

Implementing feature 3 is trivial. Add the CommandParameter property and pass it in to Execute() and CanExecute()

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:866aa6d6-9ac5-4667-8984-6a5c243d4374" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> CommandButton : Button
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> CommandButton()
    {
        Click </span><span style="color: #000000;">+=</span><span style="color: #000000;"> (sender, e) </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;">
        {
            </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (Command </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;"> </span><span style="color: #000000;">&amp;&amp;</span><span style="color: #000000;"> Command.CanExecute(CommandParameter))
                Command.Execute(CommandParameter);
        };
    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Everything else from initial Implementation</span><span style="color: #008000;">
</span><span style="color: #000000;">    
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> DependencyProperty CommandParameterProperty </span><span style="color: #000000;">=</span><span style="color: #000000;">
        DependencyProperty.Register(</span><span style="color: #800000;">"</span><span style="color: #800000;">CommandParameter</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                                    </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(</span><span style="color: #0000FF;">object</span><span style="color: #000000;">), </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(CommandButton),
                                    </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> PropertyMetadata(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">));

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">object</span><span style="color: #000000;"> CommandParameter
    {
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> GetValue(CommandParameterProperty); }
        </span><span style="color: #0000FF;">set</span><span style="color: #000000;"> { SetValue(CommandParameterProperty, value); }
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

With that, you can add parameters to the XAML:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:6b395408-f0cc-4d43-b35c-cd957419f3f2" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">local:CommandButton </span><span style="color: #FF0000;">Content</span><span style="color: #0000FF;">="Click Me"</span><span style="color: #FF0000;"> Command</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding MyCommand}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> CommandParameter</span><span style="color: #0000FF;">="MyParameter"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### 

### Hooking IsEnabled to CanExecute()

Things get a bit more complicated when implementing requirements 4 and 5, but it is still pretty straight-forward.&#160; I start by registering an event handler for when the Command property changes (CommandChanged).&#160; This event handler hooks the CanExecuteChanged event and handles the event by setting the IsEnabled flag to the value of CanExecute().&#160; It then proceeds to initialize the value of IsEnabled since we know the answer at this time.

**The final class:**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:bf466efe-a043-40c1-af0b-a8421d23d354" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> CommandButton : Button
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> CommandButton()
    {
        Click </span><span style="color: #000000;">+=</span><span style="color: #000000;"> (sender, e) </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;">
        {
            </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (Command </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;"> </span><span style="color: #000000;">&amp;&amp;</span><span style="color: #000000;"> Command.CanExecute(CommandParameter))
                Command.Execute(CommandParameter);
        };
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> DependencyProperty CommandProperty </span><span style="color: #000000;">=</span><span style="color: #000000;">
        DependencyProperty.Register(</span><span style="color: #800000;">"</span><span style="color: #800000;">Command</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                                    </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(ICommand), </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(CommandButton),
                                    </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> PropertyMetadata(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">, CommandChanged));

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> CommandChanged(DependencyObject source, DependencyPropertyChangedEventArgs args)
    {
        var button </span><span style="color: #000000;">=</span><span style="color: #000000;"> source </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> CommandButton;
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (button </span><span style="color: #000000;">==</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">) </span><span style="color: #0000FF;">return</span><span style="color: #000000;">;

        button.RegisterCommand(args.OldValue </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> ICommand, args.NewValue </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> ICommand);            
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> RegisterCommand(ICommand oldCommand, ICommand newCommand)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (oldCommand </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
            oldCommand.CanExecuteChanged </span><span style="color: #000000;">-=</span><span style="color: #000000;"> HandleCanExecuteChanged;

        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (newCommand </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
            newCommand.CanExecuteChanged </span><span style="color: #000000;">+=</span><span style="color: #000000;"> HandleCanExecuteChanged;

        HandleCanExecuteChanged(newCommand, EventArgs.Empty);
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> HandleCanExecuteChanged(</span><span style="color: #0000FF;">object</span><span style="color: #000000;"> sender, EventArgs args)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (Command </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
            IsEnabled </span><span style="color: #000000;">=</span><span style="color: #000000;"> Command.CanExecute(CommandParameter);
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ICommand Command
    {
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> GetValue(CommandProperty) </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> ICommand; }
        </span><span style="color: #0000FF;">set</span><span style="color: #000000;"> { SetValue(CommandProperty, value); }
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> DependencyProperty CommandParameterProperty </span><span style="color: #000000;">=</span><span style="color: #000000;">
        DependencyProperty.Register(</span><span style="color: #800000;">"</span><span style="color: #800000;">CommandParameter</span><span style="color: #800000;">"</span><span style="color: #000000;">,
                                    </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(</span><span style="color: #0000FF;">object</span><span style="color: #000000;">), </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(CommandButton),
                                    </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> PropertyMetadata(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">));

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">object</span><span style="color: #000000;"> CommandParameter
    {
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> GetValue(CommandParameterProperty); }
        </span><span style="color: #0000FF;">set</span><span style="color: #000000;"> { SetValue(CommandParameterProperty, value); }
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### Summary

This method for hooking commands to buttons has one drawback in my opinion:&#160; It requires you to put a CommandButton in your XAML instead of a vanilla Button.&#160; This, of course, means that other controls that inherit from Button such as Checkbox and RadioButton do not get this behavior (you have to implement this pattern for them).&#160; Still, this approach makes it very easy to add commands to any control you wish; even in WPF.&#160; In a future post, I will discuss a similar approach for binding a command to a ComboBox (or ListBox) selection changed event.