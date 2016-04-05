---
title: Adventures in MVVM – A Testing Story
id: 69
categories:
  - Uncategorized
date: 2009-05-27 18:04:00
tags:
---

[Adventures in MVVM](http://houseofbilz.com/archive/2009/05/22/adventures-in-mvvm-ndash-model-view-viewmodel.aspx) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fwww.houseofbilz.com%2Farchive%2F2009%2F05%2F27%2Fadventures-in-mvvm-ndash-a-testing-story.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-A-Testing-Story) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f05%2f27%2fadventures-in-mvvm-ndash-a-testing-story.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f05%2f27%2fadventures-in-mvvm-ndash-a-testing-story.aspx) 

I have been spending a lot of time lately thinking about client architecture with Silverlight and to a lesser extent, WPF.&#160; I tend to subscribe to the philosophy that when you make your code testable, you get a lot of other great qualities along with it.&#160; I believe this so strongly that I go out of my way to architect my inherently untestable code in a way that I can, indeed, test it.

Since I have been writing most my code for the Silverlight platform in the past several months, and I wanted very much to write my code test-driven.&#160; I used the Model-View-Presenter pattern (MVP) to implement my view as an interface.&#160; The presenter would hand model objects over to the view and I would bind visual items in my view to the model items.&#160; The presenter would then tell the IView what to do when any real logic came to play.&#160; Over time, however, more and more logic started creeping into the code behind.&#160; I had a lot of data converters to doing my dirty work, blending the code between the model and the view.&#160; The problem is that anything within the IView implementation (aka code-behind) is not easily unit-testable.

[![organic](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/MyExperiencewithMVVM_FFA6/organic_thumb.jpg "organic")](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/MyExperiencewithMVVM_FFA6/organic_2.jpg) 

As I started to really understand how binding worked in the context of XAML, I found myself wanting to use binding against view-agnostic objects for testability.&#160; Since the view didn’t know anything about the presenter, the only data to bind to was in the model.&#160; Sadly, view-specific data started to creep into model items.&#160; Where ThumbnailData (for instance) was only meta-information that came from my server, it began to gain properties such as Selected and Hidden.&#160; Moreover, what “Selected” means to one part of the UI is different to another part of the UI.

This was not the direction I wanted to go.&#160; In a fit of desperation, a colleague and I sat down and designed a pattern where we had a “ViewState” and a “ViewLogic” class.&#160; “ViewState” was just a set of properties (model data and view properties) that implements INotifyPropertyChanged for the view to bind against and “ViewLogic” manipulated the “ViewState” to add the UI smarts.

The benefits were immediate and obvious.&#160; We started seeing logic that was once in the code-behind move over to the “ViewLogic”.&#160; We immediately received testing benefits.&#160; We could write tests against the “ViewState” as if it were the actual UI.&#160; We even started writing our new features with TDD.&#160; Everything was great!

I was all ready to start blogging about it.&#160; My colleague and I had come up with this fantastic pattern for making testable Silverlight UIs and I wanted to tell the world.&#160; When doing some comparative reading I started seeing the “MVVM” term pop up here and there.&#160; It didn’t take long for me to realize that what we had invented in a bubble was just a subtle variant of the Model-View-ViewModel pattern.&#160; The ViewModel is really just our ViewLogic and ViewState combined into one single class.&#160; It only solidified my belief that this pattern had legs.

Although I sensed some initial skepticism from my team-mates about using the pattern throughout the product, it didn’t take long for them to recognize how important the MVVM pattern would be to our project.&#160; Since adoption our test coverage has skyrocketed with hundreds of useful tests.&#160; The proof is in the pudding.&#160; The vast majority of our bugs that come in have been one of two types of bugs: Missed features or improper binding – two things that the MVVM pattern can’t help prevent.

So, that is how I got so interested in the MVVM pattern.&#160; Because it is relatively new, the community is still working out the best practices.&#160; There is room for experimentation and that can be a lot of fun.&#160; Lets solve the new problems.&#160; Lets push the community in the right direction.&#160; We are already hearing the buzz for Silverlight 3 to contain native support for MVVM (via Prism).&#160; Lets keep this going… and lets have fun doing it!