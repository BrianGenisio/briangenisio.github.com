---
title: Adventures in MVVM -- Ball of Mud vs MVVM
id: 74
categories:
  - Uncategorized
date: 2009-06-19 13:43:00
tags:
---

[More Adventures in MVVM](http://houseofbilz.com/archive/2009/05/22/adventures-in-mvvm-ndash-model-view-viewmodel.aspx) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2009%2F06%2F19%2Fadventures-in-mvvm----ball-of-mud-vs-mvvm.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-Ball-of-Mud-vs-MVVM) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f06%2f19%2fadventures-in-mvvm----ball-of-mud-vs-mvvm.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f06%2f19%2fadventures-in-mvvm----ball-of-mud-vs-mvvm.aspx)

[![image](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/AdventuresinMVVMBallofMudvsMVVM_13D02/image_thumb.png "image")](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/AdventuresinMVVMBallofMudvsMVVM_13D02/image_2.png)A demo that I added to my MVVM talk is one that compares a [“Ball of Mud”](http://en.wikipedia.org/wiki/Big_ball_of_mud) application with a componentized [MVVM](http://en.wikipedia.org/wiki/Model_View_ViewModel_(MVVM)) application with the exact same set of features.&#160; I do this to contrast a messy, un-testable work of code to a cleanly separated, testable work.&#160; The application I chose to write is a Twitter search application in WPF.&#160; When you download the code, you will see two folders: “BallOfMud” and “MVVM”.&#160; 

The “Ball of Mud” app includes the entire app in a XAML file and a code-behind.&#160; The code is incredibly dense… it does a TON of stuff, all in one page of code.&#160; Unfortunately, is practically un-testable.&#160; Even if you could test it, the code hits a live twitter.com.&#160; Not the way you want to write tests.&#160; This way of writing software works well for demos, but it certainly does not scale to real-life applications.

The MVVM example, on the other hand, is significantly broken up into components (4 layers).&#160; There are 2 Views (TwitterSearchView and TweetView) with 2 corresponding ViewModels.&#160; Then, there is a Model layer which is responsible for parsing data that comes from the Service layer into “Tweets”.&#160; There is more code than the “Ball of Mud” example, but the code is testable, and modular.&#160; You can swap out different services or models, if you wish.&#160; In fact, in the Model layer, you will find a “CannedData” class that I use when I don’t have an internet connection when I give my presentation.&#160; It just swaps in.&#160; This example might feel like “overkill”, but as the application grows in scope, this architecture will scale exceptionally well.

[Twitter Search “Ball of Mud” vs “MVVM”](http://brianstestsite.googlepages.com/Twitter-BallOfMud-vs-MVVM.zip)