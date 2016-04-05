---
title: 'Adventures in MVVM – My ViewModel Base – Silverlight Support!'
tags:
  - MVVM
  - Silverlight
  - VIew Model Support
  - WPF
id: 95
categories:
  - Uncategorized
date: 2010-05-14 14:02:00
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2010%2F05%2F14%2Fadventures-in-mvvm-ndash-my-viewmodel-base-ndash-silverlight-support.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-My-ViewModel-Base-Silverlight-Support) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2010%2f05%2f14%2fadventures-in-mvvm-ndash-my-viewmodel-base-ndash-silverlight-support.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2010%2f05%2f14%2fadventures-in-mvvm-ndash-my-viewmodel-base-ndash-silverlight-support.aspx)

<font color="#ff0000">EDIT:</font> Here is why I love blogging and sharing code so much: Putting your ideas and code out into the public space always manages to add value to the ideas that were originally posted.&#160; Case in point: Tobias Richling commented on this post with a fantastic refinement to the Silverlight dynamic property binding.&#160; There is a much more simple way to bind against these dynamic properties (including automatic commanding) than my first approach.&#160; I have incorporated the changes to the CodePlex site and I have edited this post to reflect the changes.&#160; Thanks again, to Tobias for the suggestion.

In my last post, I outlined the powerful features that are available in the [ViewModelSupport](http://houseofbilz.com/archive/2010/05/08/adventures-in-mvvm-ndash-my-viewmodel-base.aspx).&#160; It takes advantage of the dynamic features of C# 4.0 (as well as some 3.0 goodies) to help eliminate the plumbing that often comes with writing ViewModels.&#160; If you are interested in learning about the capabilities, please take a look at that post and look at the code on [CodePlex](http://viewmodelsupport.codeplex.com/). 

When I wrote about the ViewModel base class, I complained that the features did not work in Silverlight because as of 4.0, it does not support binding to dynamic properties.&#160; Although I still think this is a bummer, I am happy to say that there is a workaround.&#160; In the Silverlight version of my base class, I include a string indexer for getting and setting properties that lets you bind to dynamic properties in the **ViewModelBase**, especially the convention-based commands that the base class supports.

For example, with a View Model that looks like this:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:a35f8fe8-6cfc-4037-bf17-826c5f411156" class="wlWriterSmartContent">   <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> ExampleViewModel : ViewModelBase
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> Execute_MyCommand()
    {
        Set(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Text</span><span style="color: #800000">&quot;</span><span style="color: #000000">, </span><span style="color: #800000">&quot;</span><span style="color: #800000">Foo</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

The view can bind to the dynamic property (**Text**) and the convention-based command (**MyCommand**) with the following XAML.

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:dc0daf96-5b7f-491d-b195-aa333e5cc650" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;white-space:-moz-pre-wrap; white-space: -pre-wrap; white-space: -o-pre-wrap; white-space: pre-wrap; word-wrap: break-word;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding [Text]}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> Margin</span><span style="color: #0000FF;">="5"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
</span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">Button </span><span style="color: #FF0000;">Content</span><span style="color: #0000FF;">="Execute MyCommand"</span><span style="color: #FF0000;"> Command</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding [MyCommand]}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> Margin</span><span style="color: #0000FF;">="5"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Notice the square brackets around the name of the property.&#160; Of course, it is not as perfect as binding to **Text** and **MyCommand** like you can in WPF, but it is better than having a failed feature.&#160; This allows you to share your ViewModels between WPF and Silverlight very easily. 

&lt;BeatDeadHorse&gt;Hopefully, in Silverlight 5.0, we will see binding to dynamic properties more directly????&lt;/BeatDeadHorse&gt;