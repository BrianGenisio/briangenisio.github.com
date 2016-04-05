---
title: Testing WCF Service Apps (Part 0 of 4)
id: 49
categories:
  - Uncategorized
date: 2008-11-18 17:33:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2FHouseOfBilz%2Farchive%2F2008%2F11%2F18%2Ftesting-wcf-service-apps-part-0-of-4.aspx)](http://dotnetshoutout.com/Testing-WCF-Services)   

When you are writing automated tests for your application, it is important that the tests do not rely on the file system or external services.&#160; The only problem is that when you write applications using WCF, you are writing code that relies on external services.&#160; It is very tempting to create service references in your test DLL and test against them, but please refrain.&#160; There is a better way, I promise!

This series will focus on testing every part of your application -- including round-trip functional tests -- without employing the WCF framework directly.&#160; We need to remember that the WCF framework is code that we do not control.&#160; We need to trust that it does what it claims to and focus on our code directly.&#160; In addition, the WCF framework is configurable via XML files, so we don't need to worry about having configuration working directly.&#160; Writing tests for your code needs to be about your code, not the WCF framework.

So, let's look at the application that I am using in this series.&#160; I have a WCF service named RecipeBoxService that stores and retrieves recipes.&#160; The service is used as a back end for many possible clients: WinForms, ASP.NET, MVC, Silverlight, etc.&#160; In this series, I will be writing a data mining application to demonstrate the testing techniques.

&#160;[![RecipeBox](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/TestingWCFServiceAppsPart1of4_13D06/RecipeBox_thumb.png)](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/TestingWCFServiceAppsPart1of4_13D06/RecipeBox_2.png)

This post is just the introduction.&#160; The actual parts will be laid out something like this:

1.  [Testing the WCF service](http://geekswithblogs.net/HouseOfBilz/archive/2008/11/24/testing-wcf-service-apps-part-1-of-4.aspx)2.  [Testing the synchronous client](http://houseofbilz.com/archive/2008/11/29/testing-wcf-service-apps-part-2-of-4.aspx)3.  [Testing the asynchronous client](http://www.houseofbilz.com/archive/2008/12/19/testing-wcf-service-apps-part-3-of-4.aspx)4.  [Functional testing front-to-back](http://www.houseofbilz.com/archive/2009/01/05/testing-wcf-service-apps-part-4-of-4.aspx)