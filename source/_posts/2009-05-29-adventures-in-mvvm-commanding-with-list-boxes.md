---
title: Adventures in MVVM – Commanding with List Boxes
id: 70
categories:
  - Uncategorized
date: 2009-05-29 13:41:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fgeekswithblogs.net%2FHouseOfBilz%2Farchive%2F2009%2F05%2F29%2Fadventures-in-mvvm-ndash-commanding-with-list-boxes.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-Commanding-with-List-Boxes) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2009%2f05%2f29%2fadventures-in-mvvm-ndash-commanding-with-list-boxes.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2009%2f05%2f29%2fadventures-in-mvvm-ndash-commanding-with-list-boxes.aspx) 

Continuing in my series of [“Adventures in MVVM”](http://houseofbilz.com/archive/2009/05/22/adventures-in-mvvm-ndash-model-view-viewmodel.aspx), I want to talk about a few different approaches to working with List Boxes with the MVVM pattern.&nbsp; What I am writing here is generally true of all controls that derive from Selector, including ListBox and ComboBox.&nbsp; This example was developed in Silverlight, but the same concepts also apply to WPF.

### The Problem

You have a list box in your view, and you want your ViewModel to do something when an item in the ListBox is selected. You want to do this without any code-behind, using the MVVM pattern.&nbsp; There are three methods that I have come up with, and I will outline them here.&nbsp; In this post, I will be using a VERY simple data class in my ListBox called **Person **with a First and Last name.&nbsp; It is so simple, in fact, that I have chosen not to include the source for this class.

### Method 1: Quick and Dirty SelectedItem binding

This method sets up a SelectedPerson property in the view model and does something when the property is changed.&nbsp; 
 <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:7a1ac801-440e-45ac-a937-0ce89313b546" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> ViewModel : INotifyPropertyChanged
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ObservableCollection</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Person</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> People { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> Person _selectedPerson </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">;
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> Person SelectedPerson
    {
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> _selectedPerson; }
        </span><span style="color: #0000FF;">set</span><span style="color: #000000;">
        {
            _selectedPerson </span><span style="color: #000000;">=</span><span style="color: #000000;"> value;
            OnPropertyChanged(</span><span style="color: #800000;">"</span><span style="color: #800000;">SelectedPerson</span><span style="color: #800000;">"</span><span style="color: #000000;">);    
            DoSomething(value);
        }
    }
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> ... rest of ViewModel</span><span style="color: #008000;">
</span><span style="color: #000000;">}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>
<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:e39d8690-156d-41d1-8c3d-d92da1d0d0a3" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">ListBox </span><span style="color: #FF0000;">ItemsSource</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding People}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> SelectedItem</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding SelectedPerson, Mode=TwoWay}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

**Pros: **This method is quick and simple to get going 
**Cons:** You are introducing side effects in your property code.&nbsp; If you are OK with this, then read no further.&nbsp; If this bothers you the way it does for me, then lets look at our next option.

### Method 2: Button Command

There are plenty of commanding libraries out there to choose from.&nbsp; I will take advantage of the [Prism](http://compositewpf.codeplex.com/) commanding system (Microsoft.Practices.Composite.Presentation.Commands).&nbsp; They have implemented bindable commands for ButtonBase.&nbsp; The only problem: ListBox is not a ButtonBase.&nbsp; To get around this, replace the ItemTemplate with a Button that has a template of textblock.

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:821a3512-9c0c-4b74-b6bd-faf4e9fe37e1" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> ViewModel : INotifyPropertyChanged
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ViewModel()
    {
        PersonSelected </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> DelegateCommand</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Person</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(DoSomething);
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> ... rest of constructor</span><span style="color: #008000;">
</span><span style="color: #000000;">    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ObservableCollection</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Person</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> People { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ICommand PersonSelected { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> ... rest of ViewModel    </span><span style="color: #008000;">
</span><span style="color: #000000;">}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>
<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:df1c3267-d07b-4be4-962d-615aec33d116" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">ListBox </span><span style="color: #FF0000;">ItemsSource</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding People}</span><span style="color: #0000FF;">"</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">ListBox.ItemTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
        </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">DataTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
            </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">Button </span><span style="color: #FF0000;">Commands:Click.Command</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding PersonSelected, Source={StaticResource ViewModel}}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> Commands:Click.CommandParameter</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
                </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">Button.Template</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">ControlTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
                        </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">ControlTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
                </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">Button.Template</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
            </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">Button</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
        </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">DataTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">ListBox.ItemTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
</span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">ListBox</span><span style="color: #0000FF;">&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

**Pros: **The ViewModel is much more simple with no side effects.&nbsp; 
**Cons:** The XAML is ugly as sin.&nbsp; It also changes the behavior of the ListBox in a subtle way.&nbsp; Every time you select an item, the command fires, not just when it changes.&nbsp; This is my LEAST favorite approach.&nbsp; We can do better

### Method 3: Bind Commands to the ListBox

The final mechanism is my favorite.&nbsp; Even though [Prism](http://compositewpf.codeplex.com/) doesn’t give us the ability to bind commands to ListBoxes, we can extend their attached behavior infrastructure such that all ListBoxes and ComboBoxes (or anything that derives from Selector) can take advantage of it.&nbsp; The ViewModel doesn’t change from “Method 2”, but the XAML does:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:4347b12c-780e-48d2-98c1-ff89501973fc" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">ListBox </span><span style="color: #FF0000;">ItemsSource</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding People}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> Commands:Selected.Command</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding PersonSelected}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

**Pros: **Best of both worlds.&nbsp; Simple ViewModel.&nbsp; Simple XAML 
**Cons:** You have to write some extensions to the Prism infrastructure.&nbsp; This code is boilerplate.&nbsp; I have written some generics that can reduce the boilerplate code somewhat, but not completely, due to the static properties. 

### The Winner Is….

I like “Method 3” the best.&nbsp; With a bit of some infrastructure code that you can tuck away, you get to bind the selected items to a command in any case.&nbsp; It plays well, and it is easy to follow.

But wait… you want the Prism extensions?&nbsp; Here they are:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:4f97a3a2-4440-4df0-9d8e-f62a085e3266" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> SelectorSelectedCommandBehavior : CommandBehaviorBase</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Selector</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> SelectorSelectedCommandBehavior(Selector selectableObject)
        : </span><span style="color: #0000FF;">base</span><span style="color: #000000;">(selectableObject)
    {
        selectableObject.SelectionChanged </span><span style="color: #000000;">+=</span><span style="color: #000000;"> OnSelectionChanged;
    }

    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> OnSelectionChanged(</span><span style="color: #0000FF;">object</span><span style="color: #000000;"> sender, SelectionChangedEventArgs e)
    {
        CommandParameter </span><span style="color: #000000;">=</span><span style="color: #000000;"> TargetObject.SelectedItem;
        ExecuteCommand();
    }
}</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> Selected
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> DependencyProperty SelectedCommandBehaviorProperty </span><span style="color: #000000;">=</span><span style="color: #000000;"> DependencyProperty.RegisterAttached(
        </span><span style="color: #800000;">"</span><span style="color: #800000;">SelectedCommandBehavior</span><span style="color: #800000;">"</span><span style="color: #000000;">,
        </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(SelectorSelectedCommandBehavior),
        </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(Selected),
        </span><span style="color: #0000FF;">null</span><span style="color: #000000;">);

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> DependencyProperty CommandProperty </span><span style="color: #000000;">=</span><span style="color: #000000;"> DependencyProperty.RegisterAttached(
        </span><span style="color: #800000;">"</span><span style="color: #800000;">Command</span><span style="color: #800000;">"</span><span style="color: #000000;">,
        </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(ICommand),
        </span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(Selected),
        </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> PropertyMetadata(OnSetCommandCallback));

    [System.Diagnostics.CodeAnalysis.SuppressMessage(</span><span style="color: #800000;">"</span><span style="color: #800000;">Microsoft.Design</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">CA1011:ConsiderPassingBaseTypesAsParameters</span><span style="color: #800000;">"</span><span style="color: #000000;">, Justification </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #800000;">"</span><span style="color: #800000;">Only works for selector</span><span style="color: #800000;">"</span><span style="color: #000000;">)]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> SetCommand(Selector selector, ICommand command)
    {
        selector.SetValue(CommandProperty, command);
    }

    [System.Diagnostics.CodeAnalysis.SuppressMessage(</span><span style="color: #800000;">"</span><span style="color: #800000;">Microsoft.Design</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">CA1011:ConsiderPassingBaseTypesAsParameters</span><span style="color: #800000;">"</span><span style="color: #000000;">, Justification </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #800000;">"</span><span style="color: #800000;">Only works for selector</span><span style="color: #800000;">"</span><span style="color: #000000;">)]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> ICommand GetCommand(Selector selector)
    {
        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> selector.GetValue(CommandProperty) </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> ICommand;
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> OnSetCommandCallback(DependencyObject dependencyObject, DependencyPropertyChangedEventArgs e)
    {
        var selector </span><span style="color: #000000;">=</span><span style="color: #000000;"> dependencyObject </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> Selector;
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (selector </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
        {
            GetOrCreateBehavior(selector).Command </span><span style="color: #000000;">=</span><span style="color: #000000;"> e.NewValue </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> ICommand;
        }
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> SelectorSelectedCommandBehavior GetOrCreateBehavior(Selector selector)
    {
        var behavior </span><span style="color: #000000;">=</span><span style="color: #000000;"> selector.GetValue(SelectedCommandBehaviorProperty) </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> SelectorSelectedCommandBehavior;
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (behavior </span><span style="color: #000000;">==</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
        {
            behavior </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> SelectorSelectedCommandBehavior(selector);
            selector.SetValue(SelectedCommandBehaviorProperty, behavior);
        }

        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> behavior;
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div><pre class="c-sharp" name="code"></pre>