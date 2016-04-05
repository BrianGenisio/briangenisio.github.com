---
title: Adventures in MVVM – Generalized Command Behavior Attachments
id: 76
categories:
  - Uncategorized
date: 2009-08-22 01:45:00
tags:
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/)&#160; [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2FHouseOfBilz%2Farchive%2F2009%2F08%2F21%2Fadventures-in-mvvm-ndash-generalized-command-behavior-attachments.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-Generalized-Command-Behavior-Attachments) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2fHouseOfBilz%2farchive%2f2009%2f08%2f21%2fadventures-in-mvvm-ndash-generalized-command-behavior-attachments.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2fHouseOfBilz%2farchive%2f2009%2f08%2f21%2fadventures-in-mvvm-ndash-generalized-command-behavior-attachments.aspx)

There are several examples on the web that describe the [“Attached Behavior”](http://houseofbilz.com/archive/2009/05/29/adventures-in-mvvm-ndash-commanding-with-list-boxes.aspx) pattern in Silverlight and WPF.&#160; This pattern works really well for binding commands in the ViewModel to controls in the View.&#160; The problem with this is that for every behavior, there is a LOT of boilerplate code that goes along with it.&#160; Because the DepencencyProperties need to be static, they cannot be easily abstracted into a common class. 

If you want to attach a **MouseEnterBehavior **to a control, you need to create two or three static Dependency Properties in the MouseEnter class.&#160; They are MouseEnter.Command, MouseEnter.MouseEnterBehavior and optionally, MouseEnter.CommandParameter.
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:e9ea40db-5abc-43e8-ba7b-91d3e2b0d8e3" class="wlWriterSmartContent">   <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> MouseEnter 
    {
        </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">readonly</span><span style="color: #000000"> DependencyProperty BehaviorProperty </span><span style="color: #000000">=</span><span style="color: #000000">
            DependencyProperty.RegisterAttached(
                </span><span style="color: #800000">&quot;</span><span style="color: #800000">MouseEnterBehavior</span><span style="color: #800000">&quot;</span><span style="color: #000000">,
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(MouseEnterBehavior),
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(Control),
                </span><span style="color: #0000ff">null</span><span style="color: #000000">);

        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">readonly</span><span style="color: #000000"> DependencyProperty CommandProperty </span><span style="color: #000000">=</span><span style="color: #000000">
            DependencyProperty.RegisterAttached(
                </span><span style="color: #800000">&quot;</span><span style="color: #800000">Command</span><span style="color: #800000">&quot;</span><span style="color: #000000">,
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(ICommand),
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(MouseEnter),
                </span><span style="color: #0000ff">new</span><span style="color: #000000"> PropertyMetadata(OnSetCommand));

        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">readonly</span><span style="color: #000000"> DependencyProperty CommandParameterProperty </span><span style="color: #000000">=</span><span style="color: #000000">
            DependencyProperty.RegisterAttached(
                </span><span style="color: #800000">&quot;</span><span style="color: #800000">CommandParameter</span><span style="color: #800000">&quot;</span><span style="color: #000000">,
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(</span><span style="color: #0000ff">object</span><span style="color: #000000">),
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(MouseEnter),
                </span><span style="color: #0000ff">new</span><span style="color: #000000"> PropertyMetadata(OnSetParameter))</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

And then the &quot;Change Handlers&quot;

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:816c8ecc-3aec-4f66-b672-6686ef0736e7" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #000000">        </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> OnSetCommand(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
        {
            var target </span><span style="color: #000000">=</span><span style="color: #000000"> dependencyObject </span><span style="color: #0000ff">as</span><span style="color: #000000"> Control;
            </span><span style="color: #0000ff">if</span><span style="color: #000000"> (target </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
                </span><span style="color: #0000ff">return</span><span style="color: #000000">;

            GetOrCreateBehavior(target).Command </span><span style="color: #000000">=</span><span style="color: #000000"> args.NewValue </span><span style="color: #0000ff">as</span><span style="color: #000000"> ICommand;
        }

        </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> OnSetParameter(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs args)
        {
            var target </span><span style="color: #000000">=</span><span style="color: #000000"> dependencyObject </span><span style="color: #0000ff">as</span><span style="color: #000000"> Control;
            </span><span style="color: #0000ff">if</span><span style="color: #000000"> (target </span><span style="color: #000000">!=</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
            {
                GetOrCreateBehavior(target).CommandParameter </span><span style="color: #000000">=</span><span style="color: #000000"> args.NewValue;
            }
        }

        </span><span style="color: #0000ff">protected</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> MouseEnterBehavior GetOrCreateBehavior(Control control)
        {
            var behavior </span><span style="color: #000000">=</span><span style="color: #000000"> control.GetValue(BehaviorProperty) </span><span style="color: #0000ff">as</span><span style="color: #000000"> MouseEnterBehavior;
            </span><span style="color: #0000ff">if</span><span style="color: #000000"> (behavior </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
            {
                behavior </span><span style="color: #000000">=</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> MouseEnterBehavior(control);
                control.SetValue(BehaviorProperty, behavior);
            }

            </span><span style="color: #0000ff">return</span><span style="color: #000000"> behavior;
        }</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

Since the Dependency Properties are static, Silverlight also requires you to in include Get* and Set* static methods:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:ea927050-1419-4a44-81c5-a493752ebde6" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #000000">        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> SetCommand(Control control, ICommand command) { control.SetValue(CommandProperty, command); }
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> ICommand GetCommand(Control control) { </span><span style="color: #0000ff">return</span><span style="color: #000000"> control.GetValue(CommandProperty) </span><span style="color: #0000ff">as</span><span style="color: #000000"> ICommand; }
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> SetCommandParameter(Control control, </span><span style="color: #0000ff">object</span><span style="color: #000000"> parameter) { control.SetValue(CommandParameterProperty, parameter); }
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">object</span><span style="color: #000000"> GetCommandParameter(Control buttonBase) { </span><span style="color: #0000ff">return</span><span style="color: #000000"> buttonBase.GetValue(CommandParameterProperty); }</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

This is a classic case of reuse via “Copy and Paste”.&#160; The problem is that there are several places in this code where you need to change three different types and many strings.&#160; If you don’t invoke the magic incantation properly, nothing works.&#160; It will compile but it won’t work (or you will get an obscure XAML parse error).

I cringe whenever I have to employ copy/paste reuse.&#160; In the cases where it is absolutely necessary (such as this), I believe the risk can be reduced proportionately to the complexity of the modification after you paste.&#160; This is why I came up with an **Attachment** base class to generalize all of this boilerplate code.&#160; The previous code can be reduced to:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:1f59b685-9759-4cd3-be5c-ac0e664c866e" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #000000">    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> MouseEnter : Attachment</span><span style="color: #000000">&lt;</span><span style="color: #000000">Control, MouseEnterBehavior, MouseEnter</span><span style="color: #000000">&gt;</span><span style="color: #000000">
    {
        </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">readonly</span><span style="color: #000000"> DependencyProperty BehaviorProperty </span><span style="color: #000000">=</span><span style="color: #000000"> Behavior();
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">readonly</span><span style="color: #000000"> DependencyProperty CommandProperty </span><span style="color: #000000">=</span><span style="color: #000000"> Command(BehaviorProperty);
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">readonly</span><span style="color: #000000"> DependencyProperty CommandParameterProperty </span><span style="color: #000000">=</span><span style="color: #000000"> Parameter(BehaviorProperty);

        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> SetCommand(Control control, ICommand command) { control.SetValue(CommandProperty, command); }
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> ICommand GetCommand(Control control) { </span><span style="color: #0000ff">return</span><span style="color: #000000"> control.GetValue(CommandProperty) </span><span style="color: #0000ff">as</span><span style="color: #000000"> ICommand; }
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> SetCommandParameter(Control control, </span><span style="color: #0000ff">object</span><span style="color: #000000"> parameter) { control.SetValue(CommandParameterProperty, parameter); }
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">object</span><span style="color: #000000"> GetCommandParameter(Control buttonBase) { </span><span style="color: #0000ff">return</span><span style="color: #000000"> buttonBase.GetValue(CommandParameterProperty); }
    }</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

The only modifications to this copied code exist on the first line. 

What is the class name?&#160; **MouseEnter** (2 places)

What type of control can the behavior attach to? **Control**

What type of behavior do you want to attach? **MouseEnterBehavior**

In addition to the decreased configuration complexity, the actual code that needs to be copied goes from 58 lines of boilerplate code to 11 lines of boilerplate code.&#160; This is a big win, in my opinion.

In this code, I am using the **CommandBehaviorBase** class from the [Prism](http://compositewpf.codeplex.com/) framework.&#160; It is part of the generic constraints.&#160; If you use something else for your behaviors, replace it as you see fit.&#160; Your own base class for command behaviors would slip in nicely, I am sure.

Here is the **Attachment** base class:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:90bbf315-3e80-4acf-aaf4-95a5664e59d3" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #000000">    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> Attachment</span><span style="color: #000000">&lt;</span><span style="color: #000000">TargetT, BehaviorT, AttachmentT</span><span style="color: #000000">&gt;</span><span style="color: #000000">
        </span><span style="color: #0000ff">where</span><span style="color: #000000"> TargetT : Control
        </span><span style="color: #0000ff">where</span><span style="color: #000000"> BehaviorT : CommandBehaviorBase</span><span style="color: #000000">&lt;</span><span style="color: #000000">TargetT</span><span style="color: #000000">&gt;</span><span style="color: #000000">
    {
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> DependencyProperty Behavior()
        {
            </span><span style="color: #0000ff">return</span><span style="color: #000000"> DependencyProperty.RegisterAttached(
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(BehaviorT).Name,
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(BehaviorT),
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(TargetT),
                </span><span style="color: #0000ff">null</span><span style="color: #000000">);
        }

        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> DependencyProperty Command(DependencyProperty behaviorProperty)
        {
            </span><span style="color: #0000ff">return</span><span style="color: #000000"> DependencyProperty.RegisterAttached(
                </span><span style="color: #800000">&quot;</span><span style="color: #800000">Command</span><span style="color: #800000">&quot;</span><span style="color: #000000">,
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000"> (ICommand),
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000">(AttachmentT),
                </span><span style="color: #0000ff">new</span><span style="color: #000000"> PropertyMetadata((target, args) </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> OnSetCommandCallback(target, args, behaviorProperty)));
        }

        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> DependencyProperty Parameter(DependencyProperty behaviorProperty)
        {
            </span><span style="color: #0000ff">return</span><span style="color: #000000"> DependencyProperty.RegisterAttached(
                </span><span style="color: #800000">&quot;</span><span style="color: #800000">CommandParameter</span><span style="color: #800000">&quot;</span><span style="color: #000000">,
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000"> (</span><span style="color: #0000ff">object</span><span style="color: #000000">),
                </span><span style="color: #0000ff">typeof</span><span style="color: #000000"> (AttachmentT),
                </span><span style="color: #0000ff">new</span><span style="color: #000000"> PropertyMetadata((target, args) </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> OnSetParameterCallback(target, args, behaviorProperty)));
        }

        </span><span style="color: #0000ff">protected</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> OnSetCommandCallback(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e, DependencyProperty behaviorProperty)
        {
            var target </span><span style="color: #000000">=</span><span style="color: #000000"> dependencyObject </span><span style="color: #0000ff">as</span><span style="color: #000000"> TargetT;
            </span><span style="color: #0000ff">if</span><span style="color: #000000"> (target </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
                </span><span style="color: #0000ff">return</span><span style="color: #000000">;

            GetOrCreateBehavior(target, behaviorProperty).Command </span><span style="color: #000000">=</span><span style="color: #000000"> e.NewValue </span><span style="color: #0000ff">as</span><span style="color: #000000"> ICommand;
        }

        </span><span style="color: #0000ff">protected</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> OnSetParameterCallback(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e, DependencyProperty behaviorProperty)
        {
            var target </span><span style="color: #000000">=</span><span style="color: #000000"> dependencyObject </span><span style="color: #0000ff">as</span><span style="color: #000000"> TargetT;
            </span><span style="color: #0000ff">if</span><span style="color: #000000"> (target </span><span style="color: #000000">!=</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
            {
                GetOrCreateBehavior(target, behaviorProperty).CommandParameter </span><span style="color: #000000">=</span><span style="color: #000000"> e.NewValue;
            }
        }

        </span><span style="color: #0000ff">protected</span><span style="color: #000000"> </span><span style="color: #0000ff">static</span><span style="color: #000000"> BehaviorT GetOrCreateBehavior(Control control, DependencyProperty behaviorProperty)
        {
            var behavior </span><span style="color: #000000">=</span><span style="color: #000000"> control.GetValue(behaviorProperty) </span><span style="color: #0000ff">as</span><span style="color: #000000"> BehaviorT;
            </span><span style="color: #0000ff">if</span><span style="color: #000000"> (behavior </span><span style="color: #000000">==</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
            {
                behavior </span><span style="color: #000000">=</span><span style="color: #000000"> Activator.CreateInstance(</span><span style="color: #0000ff">typeof</span><span style="color: #000000">(BehaviorT), control) </span><span style="color: #0000ff">as</span><span style="color: #000000"> BehaviorT;
                control.SetValue(behaviorProperty, behavior);
            }

            </span><span style="color: #0000ff">return</span><span style="color: #000000"> behavior;
        }
    }</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

And finally, just to complete the example, here is what the **MouseEnterBehavior** looks like:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:8851b117-15d5-4c8a-972f-8100b278b52f" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #000000">    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> MouseEnterBehavior : CommandBehaviorBase</span><span style="color: #000000">&lt;</span><span style="color: #000000">Control</span><span style="color: #000000">&gt;</span><span style="color: #000000">
    {
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> MouseEnterBehavior(Control selectableObject)
            : </span><span style="color: #0000ff">base</span><span style="color: #000000">(selectableObject)
        {
            selectableObject.MouseEnter </span><span style="color: #000000">+=</span><span style="color: #000000"> (sender, args) </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> ExecuteCommand();
        }
    }</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

And to use it in your XAML, it would look like this:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:8f041e8a-6219-4e9f-a156-fa06965082e7" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">&lt;</span><span style="color: #800000">Button </span><span style="color: #ff0000">Behaviors:MouseEnter.Command</span><span style="color: #0000ff">=&quot;</span><span style="color: #808000">{Binding MouseEnter}</span><span style="color: #0000ff">&quot;</span><span style="color: #ff0000"> Behaviors:MouseEnter.CommandParameter</span><span style="color: #0000ff">=&quot;Optional Paremeter&quot;</span><span style="color: #0000ff">/&gt;</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>