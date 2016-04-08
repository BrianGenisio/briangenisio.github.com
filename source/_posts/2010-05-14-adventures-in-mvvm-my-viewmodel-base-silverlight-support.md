---
title: 'Adventures in MVVM – My ViewModel Base – Silverlight Support!'
date: 2010-05-14 14:02:00
layout: post
category: Software
tags: [MVVM, Silverlight, View Model Support, WPF]
permalink: /archives/2010/05/14/adventures-in-mvvm-my-viewmodel-base-silverlight-support/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/)

<font color="#ff0000">EDIT:</font> Here is why I love blogging and sharing code so much: Putting your ideas and code out into the public space always manages to add value to the ideas that were originally posted.&#160; Case in point: Tobias Richling commented on this post with a fantastic refinement to the Silverlight dynamic property binding.&#160; There is a much more simple way to bind against these dynamic properties (including automatic commanding) than my first approach.&#160; I have incorporated the changes to the CodePlex site and I have edited this post to reflect the changes.&#160; Thanks again, to Tobias for the suggestion.

In my last post, I outlined the powerful features that are available in the [ViewModelSupport](/archive/2010/05/08/adventures-in-mvvm-ndash-my-viewmodel-base.aspx).&#160; It takes advantage of the dynamic features of C# 4.0 (as well as some 3.0 goodies) to help eliminate the plumbing that often comes with writing ViewModels.&#160; If you are interested in learning about the capabilities, please take a look at that post and look at the code on [CodePlex](http://viewmodelsupport.codeplex.com/). 

When I wrote about the ViewModel base class, I complained that the features did not work in Silverlight because as of 4.0, it does not support binding to dynamic properties.&#160; Although I still think this is a bummer, I am happy to say that there is a workaround.&#160; In the Silverlight version of my base class, I include a string indexer for getting and setting properties that lets you bind to dynamic properties in the **ViewModelBase**, especially the convention-based commands that the base class supports.

For example, with a View Model that looks like this:

```csharp
public class ExampleViewModel : ViewModelBase
{
    public void Execute_MyCommand()
    {
        Set("Text", "Foo");
    }
}
```

The view can bind to the dynamic property (**Text**) and the convention-based command (**MyCommand**) with the following XAML.

```xml
<TextBlock Text="{Binding [Text]}" Margin="5" />
<Button Content="Execute MyCommand" Command="{Binding [MyCommand]}" Margin="5" />
```

Notice the square brackets around the name of the property.&#160; Of course, it is not as perfect as binding to **Text** and **MyCommand** like you can in WPF, but it is better than having a failed feature.&#160; This allows you to share your ViewModels between WPF and Silverlight very easily. 

&lt;BeatDeadHorse&gt;Hopefully, in Silverlight 5.0, we will see binding to dynamic properties more directly????&lt;/BeatDeadHorse&gt;