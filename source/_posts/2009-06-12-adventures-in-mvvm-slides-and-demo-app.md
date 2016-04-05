---
title: Adventures in MVVM – Slides and Demo App
id: 73
categories:
  - Uncategorized
date: 2009-06-12 13:42:00
tags:
---

[More Adventures in MVVM](http://houseofbilz.com/archive/2009/05/22/adventures-in-mvvm-ndash-model-view-viewmodel.aspx)

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fgeekswithblogs.net%2FHouseOfBilz%2Farchive%2F2009%2F06%2F12%2Fadventures-in-mvvm-ndash-slides-and-demo-app.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-Slides-and-Demo-App) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2009%2f06%2f12%2fadventures-in-mvvm-ndash-slides-and-demo-app.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2009%2f06%2f12%2fadventures-in-mvvm-ndash-slides-and-demo-app.aspx)

Yesterday, I gave my MVVM talk to the public for the first time in [Flint, MI](http://www.glugnet.org/).&#160; I will be in Southfield next week and Tennessee the week after that (CodeStock), giving the same talk.&#160; Although I have some changes to make, I thought I would post what I presented last night.&#160; I will probably work on a smaller example that I will also put up here when I am done with it. 

### The Demo
 ![](http://brianstestsite.googlepages.com/RecipeBox.png)   

The demo is a front-end to a recipe database.&#160; It should compile and run out of the box.&#160; It was designed to work against [www.eCuisine.com](http://www.eCuisine.com), but it requires you to sign up with them in order to get real data.&#160; To get around that, I have stored some “canned” recipes in the service by default.&#160; If you want to hook it up to use the real eCuisine.com service, simply register with them from [www.eCuisine.com](http://www.eCuisine.com) and add your email/client ID in the eCuisine.svc.cs file and uncomment the null for the service.&#160; If you don’t do that, everything still works well enough for you to get the feel for the app.

The vast majority of the UI behavior exists in the View-Models and I show a few different approaches to getting View-Models hooked up to the views.

There is also a full suite of tests.&#160; Be sure to check them out for ideas to how you might test your View-Models.

[**Demo Code**](http://brianstestsite.googlepages.com/RecipeBox.Public.zip)

**[Powerpoint Slides](http://brianstestsite.googlepages.com/MVVM.ppt)**