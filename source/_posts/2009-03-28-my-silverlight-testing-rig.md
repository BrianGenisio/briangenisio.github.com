---
title: My Silverlight Testing Rig
id: 62
categories:
  - Uncategorized
date: 2009-03-28 17:53:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fwww.houseofbilz.com%2Farchive%2F2009%2F03%2F28%2Fmy-silverlight-testing-rig.aspx)](http://dotnetshoutout.com/My-Silverlight-Testing-Rig) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f03%2f28%2fmy-silverlight-testing-rig.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f03%2f28%2fmy-silverlight-testing-rig.aspx)   

I was recently asked by someone on Twitter to describe my setup for writing tests against my Silverlight application.&#160; I thought I would write them down here in case others might find this information useful.

**Testing Framework: **A slightly [modified version of NUnit](http://weblogs.asp.net/nunitaddin/archive/2008/05/01/silverlight-nunit-projects.aspx) that works with SIlverlight.

**Mocking Framework:** [Moq 3.0](http://code.google.com/p/moq/) includes support for Silverlight.&#160; This is my favorite mocking framework, so I am happy that it now supports Silverlight.

**Test Runner: **I use [TestDriven.NET](http://testdriven.net/) to run all of my tests locally.&#160; I use [Cruise Control](http://confluence.public.thoughtworks.org/display/CCNET/Welcome+to+CruiseControl.NET) to run the tests on the “Continuous Integration” system.

**Making testing easier: **This is not specific to Silverlight testing, but I use [ReSharper](http://www.jetbrains.com/resharper/index.html) templates that I have customized to aid in writing tests quickly.

**The MVVM Pattern: **This is not a tool, but I the majority of my SIlverlight code would be un-testable if I didn’t know the [MVVM](http://www.orbifold.net/default/?p=550) pattern.&#160; I highly recommend using this pattern with Silverlight.&#160; None of the view code can be tested using NUnit, so testing the ViewModel is essential.

**UI Automation: **I am just starting to play with this.&#160; [White](http://www.codeplex.com/white) is a layer on top of Microsoft’s UI Automation (UIA) framework.&#160; UIA is much to hard to use without a “helper” layer.&#160; It lets you run fully automated UI tests.&#160; I only want to use this to make sure my data bindings are correct.&#160; My unit tests are really the bulk of my testing efforts.