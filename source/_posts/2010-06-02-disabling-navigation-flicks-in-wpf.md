---
title: Disabling Navigation Flicks in WPF
date: 2010-06-02 12:32:00
layout: post
category: Software
tags: [Navigation, WPF]
permalink: /archives/2010/06/02/disabling-navigation-flicks-in-wpf/
---

I am currently working on a multi-touch application using WPF.&nbsp; One thing that has been irritating me with this development is an automatic navigation forward/back command that is bound to forward and backwards flicks.&nbsp; Many of my touch-based interactions were being thwarted by gestures picked up by WPF as navigation.&nbsp; I just wanted to disable this behavior. 

My programmatic back/forward calls are not affected by this change, which is nice.&nbsp; Here is how I did it:&nbsp; In my main window, I added the following command bindings:

```xml
<NavigationWindow.CommandBindings>
    <CommandBinding Command="NavigationCommands.BrowseBack" Executed="DoNothing" />
    <CommandBinding Command="NavigationCommands.BrowseForward" Executed="DoNothing" />
</NavigationWindow.CommandBindings>
```
Then, the **DoNothing **method in the code-behind does nothing:

```csharp
private void DoNothing(object sender, ExecutedRoutedEventArgs e) { }
```

There may be a better way to do this, but I haven’t found one.