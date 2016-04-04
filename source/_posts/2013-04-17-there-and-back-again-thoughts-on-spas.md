---
layout: post
title: "There and Back Again -- Thoughts on SPAs"
description: "Reflections on a Career in Software"
category: Software
tags: [Angular, Backbone, SPA]
permalink: software/2013/04/17/there-and-back-again-thoughts-on-spas.html
---

I've been doing this software thing for quite a while now.  My first software job was in web development back in 1998.  Since then, I've traveled through Simulation Systems, Embedded Systems, Tooling, Desktop Applications, Rich Internet Applications (RIA), some mobile and today I am back where I started, developing Web Applications.

### Productivity Highs with RIA Frameworks
After traveling out and back, I see everything from a different perspective.  Web applications, today, are incredibly different from the way they were in the 90s.  The biggest difference, from my perspective, is the way we develop them.  Coming from the RIA perspective, using Silverlight and Flex, I am accustomed to a certain productivity that I pine for now that I am developing for the web again.

If you know me, you know that I was a big fan of Silverlight and Flex.  They were frameworks which let me develop amazing desktop experiences in the browser.  I was doing things in 2007 that to this day is still impossible to do with HTML5 today.  Those frameworks had one flaw, however.  They required plug-ins to run.

### Death to the Plug-In
I've never been much of an apologist for plug-ins. I saw them as a necessary evil.  An evil that became unsustainable in this brave new world of mobile computing.  When Apple, Microsoft and Adobe killed Silverlight and Flex, I wasn't sad because I loved those platforms.  I was sad because I knew I couldn't be as productive writing the same kinds of applications in HTML/JavaScript/CSS.

### First Generation SPA Frameworks
This is where the Single-Paged Application (SPA) frameworks came to my aid.  There are too many to count, but from my vantage point, it has felt like Backbone.js and Knockout.js have been the primary options in the SPA market.    I landed on Backbone.js as my go-to choice because it was a more complete solution than Knockout.js with its client-side route handling and model synchronization.  I found myself at home with building SPAs because they were so similar to RIAs.

Unfortunately, my productivity never came close to that of the plug-in frameworks.  Backbone.js and Knockout.js simply aren't high enough of an abstraction for them to provide the same abilities to me.  They are the first generation of SPA tools and with that brings the pains of a first-generation technology.

### Filling in the Gaps
We're starting to see some pretty nice add-ons to our first-generation tools.  These add-ons fill the gaps that the frameworks leave by attempting to make you more productive.  Specifically, Backbone.js has a poor view abstraction, making things like collection views and deeply nested views difficult.  Marionette.js builds upon Backbone.js to provide more structure, guidance and capabilities.  Using Marionette.js boosted my productivity significantly by taking care of much of the boiler-plate code that I found myself writing with Backbone.js alone.  It is still not ideal, mostly because the abstractions are not extremely strong.  They are strongER, but nothing compared to the capabilities I pine for.  Knockout.js has a similar packaging called Hot Towel.  Hot Towel fills in the holes that Knockout.js left by providing model synchronization, route handling and child view handling.

Unfortunately, these approaches only take us as far as the base technologies allow us to.  Without a strong abstraction to begin with, these add-ons can only ever takes us to generation 1.5.

### Second Generation SPA Frameworks
At the same time, the second generation of the SPA frameworks are coming forward.  They come from a higher level of abstraction and the code you write demonstrates it by being significantly less (half or less).  These frameworks (Angular.js and Ember.js) provide everything we wanted from our first generation tools but they also bring more capabilities like convention-based development paradigms and dependency injection.  What's more, we are starting to see tooling (such as Batarang) built around these abstractions which let our development lifecycle be that much smoother.

I am focusing my efforts on these second-generation tool sets for now.  I am not abandoning my Backbone.js/Marionette.js tools completely, but I am not planning to invest much more time in them.  Instead of patching the holes that the first generation tools created, I am choosing to spend my time using (and recommending) the next generation of tools.  Specifically, I am focusing my efforts on Angular.js.  It fits my development style better and I feel like the abstractions make more sense to me.  I'm not knocking on Ember.js in any way.  It is a fine choice, in my opinion.

### The Future of SPA Development
But this isn't where we are settling.  I think it will require a third generation of tools before the SPA space really takes off.  I have no idea what these frameworks will be, but I expect they will approach the productivity that I grew accustomed to with Silverlight and Flex.  The levels of abstractions will be higher and the tooling will be richer.  I expect that we will start to approach similar developer/designer workflows that were so amazing in the SPA days.  I expect we will see multiple language integration, including debugging and profiling tools built around these frameworks.  They will feel much less like a web framework and more like a desktop development framework.  The user experiences we create will be comparable to the experiences we created with Flex and Silverlight.

### Paying Back the Debt
The funny thing is that we will have taken 5 years to get there.  It will be 5 years of stepping out of the plug-ins and into the browser so that we can achieve the same quality of application that we were already producing.  In some ways, it feels like a regression, but we have to realize that the plug-ins were really just a disguised form of technical debt.  They provided a one-off environment so that we were able to do things that weren't possible in the browser, but they did so by not investing in the browser.  Ultimately, the browser is the platform we were headed towards so we incurred debt.  We just have to pay it off before we can really innovate again.

### Conclusion
I'm excited to see where the future of front-end development goes.  I'm excited to see that great strides are being made and I am happy to be at the front of the wave.  The community is starting to get excited too.  Here in Ann Arbor, we will be kicking off a JavaScript user group very soon which will give us a place to discuss these technologies and approaches.  I look forward to chatting it up!

### On a personal note
This is my last blog post written for SRT Solutions.  I am moving on to something new.  More on that soon.