---
title: 'Cross-Training in Silverlight & Flex – The Future of RIAs'
date: 2010-10-30 01:31:36
layout: post
category: Software
tags: [Cross-Training, Flex, RIA, Silverlight, Future]
permalink: /archives/2010/10/30/cross-training-in-silverlight-flex-the-future-of-rias/
---

More [Cross-Training in Silverlight &amp; Flex](/archives/2010/10/24/cross-training-in-silverlight-vs-flex/)


What a week for RIAs!&#160; The first half of the week was consumed by the [Adobe MAX](http://max.adobe.com/) conference where we got to see the future of Flex.&#160; The second half of the week was consumed by the [Microsoft PDC](http://player.microsoftpdc.com/session) conference where many of us were hoping to hear about the future of Silverlight.&#160; Unfortunately, the PDC conference was noticeably absent of Silverlight futures.&#160; This caused a ton of speculation about the future of Silverlight.&#160; 

One of the most prominent articles of the day was written by the prolific technical journalist, Mary-Jo Foley – [Microsoft: Our strategy with Silverlight has shifted.](http://www.zdnet.com/blog/microsoft/microsoft-our-strategy-with-silverlight-has-shifted/7834)&#160; This article cause a huge uproar which then drew many people to weigh in on the topic (Is Silverlight dead?&#160; Is Microsoft abandoning SIlverlight?&#160; Is this the death of the RIA framework?).&#160; Many blog posts have come out with plenty of opinions, but I think I have a different perspective.&#160; One that looks further into the future and allows for peaceful co-existence between Silverlight, Flex and HTML5.

<font color="#ff0000">What I am about to write is purely speculative.&#160; I am making predictions. Predictions are very often wrong.&#160; Still, I think what I have to say adds to the debate.</font>

### <font color="#000000" face="Arial">Let’s Stop Thinking of Flex and SIlverlight as Separate Platforms</font>

The browser is a platform.&#160; With the introduction of HTML5 standards, we have all of the primitive structures that are required for a client platform.&#160; The browser is ALSO a platform that lives on every web-enabled device we own.&#160; Because of this, many have made the brazen statement: “We don’t need RIA technologies (Flex and Silveright specifically).&#160; All we need is HTML5 and Javascript”.&#160; 

If you think of Flex and Silverlight as separate platforms, I think I might agree with that.&#160; The browser is everywhere.&#160; Flash and SIlverlight are not.&#160; But is that REALLY where we are headed?&#160; Or did the RIA plug-in come into existence because we needed to shoehorn something before wide-spread adoption of HTML5?&#160; What if Flex and Silverlight were frameworks that sit on top of the browser platform instead of being their own platforms that compete with the browser?

Put another way, I will ask this question: “Why do we need Silverlight and Flash plug-ins?”&#160; This is a serious question.&#160; Now that we have all of the primitive components necessary in the browser to do everything we need, why can’t Flex and Silverlight compile down to HTML5 and Javascript?

Sound crazy?&#160; I’ve been accused of this idea being crazy… but I don’t think it is.&#160; Microsoft proved that managed C# can be compiled down to Javascript (as opposed to byte code) over two years ago with their [Volta experiment](http://en.wikipedia.org/wiki/Microsoft_Live_Labs_Volta).&#160; Google has done similar things with Java.&#160; What is to say that Silverlight IL or Flex ActionScript bytecode can’t be compiled to Javascript?&#160; Continue with that reasoning and XAML or MXML (the declarative UIs of the RIA platforms) can be translated to the DOM.&#160; All of the control sets can be delivered as Javascript libraries that build upon existing HTML controls, or build up new ones with the canvas.&#160; **Everything can run natively on the HTML5 engine.&#160; Get rid of the plug-ins!**

### Silverlight and Flex as Frameworks

In a plug-in free world, we are left to develop in any language and any framework we want.&#160; Silverlight and Flex become higher-level abstractions on the virtual machine that is HTML5.&#160; Developers are given choices – not just HTML and Javascript, but XAML and C#, MXML and ActionScript, HAML and Ruby, FooMark and MyLang, whatever.&#160; You won’t have to be confined to a single markup, control set and programming language.

In other words, if we start to look at the browser like a virtual machine, our RIA possibilities are endless.&#160; What if HTML6 defined a bytecode format that the frameworks compiled to?&#160; Skip the Javascript.&#160; We can follow this idea to many conclusions.
  > <font color="#0000ff">**EDIT:** To qualify my statements further, I want to be clear: I don’t expect Silverlight and Flex projects will translate directly to HTML5/JS.&#160; I think that Silverlight and Flex frameworks will become native browser frameworks (System.Core.js, or Flex.js for example).&#160; Silverlight and Flex applications will run with the frameworks in the browser without a plug-in.&#160; Doing so allows for these frameworks to be portable to all platforms (phones, tablets, set-tops, browsers and desktops).&#160; </font>  

### So, is Silverlight Dead?

I don’t think so.&#160; I really believe that SIlverlight is shifting towards being a development framework, instead of a development platform.&#160; Look at the WIndows Phone 7, for example.&#160; The phone OS is the platform.&#160; Silverlight is the framework.&#160; Adobe is certainly looking at it this way.&#160; They have already created a tool that will compile Air apps down to the native iOS (iPhone) platform.&#160; In addition, they have announced that Air will be available on every device that we care about (iPhone, Android, Blackberry, WebOS, GoogleTV, etc).&#160; I **fully** expect Silverlight to do the same.

Somebody on Twitter said “They didn’t mention me at the PDC. Does that mean I’m dead?”.&#160; A witty way to say it, but I think it rings true.&#160; They didn’t mention Silverlight specifically, probably because they were focusing on other things.&#160; They are taking a huge gamble on the phone, and I think it is necessary for the future of SIlverlight as a framework.&#160; We will hear more about SIlverlight.&#160; I am sure of it.&#160; **My take: Silverlight isn’t dead.&#160; It is only evolving.**

What do you think?&#160; Am I crazy? (I might be) If so, tell me why.&#160; 