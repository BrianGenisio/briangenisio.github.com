---
title: Adventures in MVVM – CodeMash Schedule Application
date: 2010-01-11 13:56:00
layout: post
category: Software
tags: [CodeMash, MVVM]
permalink: /archives/2010/01/11/adventures-in-mvvm-codemash-schedule-application/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/)

So, I am getting myself ready to head out to Sandusy, Ohio for three days of nerdy fun at [CodeMash](http://Codemash.org).&#160; While I am there, I will be giving my “Introduction to the MVVM Pattern”.&#160; In preparation for this talk, I decided to create a new demo application.&#160; This application is a calendar and session list for the conference.&#160; It is not terribly fancy (I wish I had more time (or talent) to skin it better), but it highlights many of the concepts that I will be speaking about in my presentation.

The app is a Silverlight 3 project utilizing the Telerik Scheduler control.&#160; This particular control is rather rich, and I was excited to get my hands dirty with it. The ViewModel for the scheduler was primarily responsible for translating session data into something the Telerik control could consume.&#160;&#160; The app also includes a session list with a filter panel which is derived from the session data.&#160; This is where the majority of the MVVM code can be found.&#160; The ViewModels for the session list are responsible for dynamically creating and merging filter predicates based on the values in the session list.

[![CodeMashSessions.Calendar](/images/CodeMashSessions.Calendar.png "CodeMashSessions.Calendar")](/images/CodeMashSessions.Calendar.png) [![CodeMashSessions.Sessions](/images/CodeMashSessions.Sessions.png "CodeMashSessions.Sessions")](/images/CodeMashSessions.Sessions.png)


CodeMashSessions Source Code: [CodeMashSessions.Source.zip](/assets/CodeMashSessions.Source.zip)

Twitter &quot;Ball of Mud&quot; vs MVVM Source Code: [Twitter-BallOfMud-vs-MVVM.zip](/assets/Twitter-BallOfMud-vs-MVVM.zip)

Slides from &quot;An Introduction to the MVVM Pattern&quot;: [MVVM.ppt](/assets/MVVM.ppt)