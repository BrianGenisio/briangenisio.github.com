---
title: My Silverlight 3 Wish List
id: 60
categories:
  - Uncategorized
date: 2009-03-06 17:51:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fwww.houseofbilz.com%2Farchive%2F2009%2F03%2F06%2Fmy-silverlight-3-wish-list.aspx)](http://dotnetshoutout.com/My-Silverlight-3-Wish-List) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f03%2f06%2fmy-silverlight-3-wish-list.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f03%2f06%2fmy-silverlight-3-wish-list.aspx)   

I have been developing full time in Silverlight 2 for a few months now.&#160; I must say, it is the most fun environment I have ever programmed in.&#160; It has been an enabling technology in our product, and I am rather excited to see what comes next.&#160; If the pattern continues as I expect it probably will, we will hear all about the features of Silverlight 3 in a few weeks at [Mix 09](http://visitmix.com/).&#160; I will not be attending this conference, but I fully expect to be watching the important sessions virtually.&#160; I am hoping for a lot.&#160; You see, as much as I like the SIlverlight framework, it is missing some significant features that I can really use.

So, I figured I would publish my Silverlight 3 wish list.&#160; Some of these things are more important than others, but all of them are things that would make my &quot;Rich Internet Application&quot; more rich. **I don't want this list to sound like I am just a big complainer.&#160; **I think I am exercising the framework pretty hard, and Microsoft can only make the framework better with this list.&#160; 

**MediaElement frame rate control: **The Silverlight media element is missing the ability to control the frame rate.&#160; This keeps us from controlling the playback for slow-motion or fast-forward.&#160; I have to imagine that the sports domain wants this as well.

**MediaElement loop control: **The current media element does not support looping of video content.&#160; It can be programmed by hooking the &quot;MediaEnded&quot; event, putting the position at zero, and playing again, but this causes a noticeable pause.&#160; This pause keeps looping play from being fluid.&#160; It would be nice if the media element supported it directly.

**Native video rendering: **Silverlight 2 does not use any hardware capabilities for video playback -- it is all done in software.&#160; Therefore, the playback takes a lot of processor power.&#160; This is really noticeable when four videos are playing simultaneously.&#160; Any more on modern hardware is often choppy.&#160; I would like to be able to play as many as nine videos at once.

**3D support: **Similar to the previous wish, Silverlight 2 does not use the video hardware directly.&#160; This means that there is no 3D support.&#160; It would be nice to see some 3D capabilities in Silverlight 3 -- preferably handled by hardware.

**Direct bitmap manipulation: **Silverlight 2 does not support this, and there are certain types of effects and manipulations that are not possible in Silverlight.&#160; I would love to see some bitmap manipulation capabilities

**Support for other data transports: **Currently, only HTTP requests are made available through the SIlverlight plug-in.&#160; This limits the use of more sophisticated WCF bindings.&#160; At a very minimum, it would be nice to be able to use TCP bindings via WCF.

**Better mouse support: **Currently, we need to [hack in right-click](http://www.houseofbilz.com/archive/2009/03/01/right-clicking-in-silverlight-2----a-refinement.aspx), double-click and scroll-wheel capabilities.&#160; It would be nice to have native support.

**Support for drag-and-drop: **This is another case where I have [hacked something together](http://www.houseofbilz.com/archive/2009/02/10/drag-and-drop-with-silverlight.aspx), but it would be great if I were able to rely on the framework.

**Native support for the MVVM pattern: **As far as I have figured, the MVVM pattern is the most suitable pattern for most large-scale Silverlight development.&#160; It allows us to test the majority of our code and keep it out of the view.&#160; Unfortunately, I haven't found MVVM to be well supported in the framework.

*   Commands -- They gave us ICommand, but nothing else.&#160; I have hacked something in, but native support would be nice.*   See what [Silverlight.FX](http://projects.nikhilk.net/SilverlightFX/) and [Cliburn](http://www.codeplex.com/caliburn) have done.&#160; Can we get something like this in the framework?*   Some sort of &quot;Inversion of Control&quot; or similar decoupling support?&#160; Might MEF or Prism show up in SIlverlight 3?*   Stand-alone binding -- As far as I can tell, binding is only available in controls.&#160; I can't write tests against controls.&#160; I often want to bind data in my model to my view model.&#160; I have written my own binder for this, but it would be nice to have some built-in support*   Better property notification.&#160; INotifyPropertyChanged creates a lot of boiler-plate code in the view model.&#160; I have minimized this by using a &quot;property bag&quot;, but some utilities that make view models easier to write would be nice.  

**Interprocess communication: **I would like the ability to talk to other non-silverlight processes.&#160; This seems like a tall order, but I have some business cases for this.

That is all I have for now.&#160; I am sure there is more that I am not thinking of.&#160; I come across different needs every day.&#160; For most cases, I have been able to hack some sort of support in when I need it.&#160; In some cases, like the video playback support, I can't hack my wan out of the limitations.&#160; 

This list looks like a lot now that I have written it down.&#160; I am very curious to know if I will be getting any of these features.