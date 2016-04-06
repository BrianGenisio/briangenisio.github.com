---
title: 'Cross-Training in Silverlight & Flex – Platform Overview'
layout: post
category: Software
tags: [Cross-Training, Flex, RIA, Silverlight]
permalink: /archives/2010/10/26/cross-training-in-silverlight-flex-platform-overview/
---

More [Cross-Training in SIlverlight &amp; Flex](/archives/2010/10/24/cross-training-in-silverlight-vs-flex/)

This is the second article in which I will compare the technical details between Silverlight and Flex. Before I jump into the nitty-gritty of the two, I thought I would give a general overview of the platforms that are Silverlight and Flex/Flash/Air. Understand these terms and the tools up front will make it much easier to cross-train on the two technologies.

### The Silverlight Platform

Microsoft created [Silverlight](http://www.silverlight.net/) as a small platform for developing Rich Internet Applications ([RIAs](http://en.wikipedia.org/wiki/Rich_Internet_application)) on the desktop, browser and mobile phone. Although it was inspired by the [.Net](http://www.microsoft.com/net/) platform and the Windows Presentation Foundation ([WPF](http://msdn.microsoft.com/en-us/library/aa970268.aspx)), Silverlight stands alone. In other words, users do not need to download the entire .Net runtime to execute Silverlight applications. They only need the small (about 4 MB) Silverlight player.&#160; 

As [Flex](http://www.adobe.com/products/flex/) is a framework that sits on top of the [Flash](http://www.adobe.com/products/flash/) platform, Silverlight is both – a runtime and an SDK. Silverlight applications can be developed to run “out of browser” on the desktop (similar to [Adobe Air](http://www.adobe.com/products/air/)) or in-browser (similar to Flash Player). When run out of the browser (OOB), the application has access to extra APIs for file access and other system-level functions. Silverlight is also the primary application development platform for the [Windows Phone 7](http://www.microsoft.com/windowsphone). 

#### 

#### Developer Story

Much like Flex, Silverlight employs a declarative UI mechanism ([XAML](http://en.wikipedia.org/wiki/XAML)) for defining the view of an application. Unlike Flex, Silverlight developers have the choice of many languages to integrate in their “code-behind” such as [C#](http://en.wikipedia.org/wiki/C_Sharp_(programming_language)), [Visual Basic.Net](http://en.wikipedia.org/wiki/Visual_Basic_.NET), [F#](http://en.wikipedia.org/wiki/F_Sharp_(programming_language)), [IronRuby](http://en.wikipedia.org/wiki/Ironruby) and [IronPython](http://en.wikipedia.org/wiki/Ironpython). The majority of Silverlight developers use C#.

#### IDEs

Most Silverlight applications are developed using a version of [Visual Studio](http://www.microsoft.com/visualstudio/en-us/visual-studio-2010-launch?WT.mc_id=SEARCH&amp;WT.srch=1), which is an [IDE](http://en.wikipedia.org/wiki/Integrated_development_environment) similar to [Flash Builder](http://www.adobe.com/products/flashbuilder/).&#160; Visual Studio comes in many flavors. [Visual Web Developer Express](http://www.microsoft.com/web/gallery/install.aspx?appid=silverlight4tools;silverlight4toolkit;riaservicestoolkit) is free and great for getting started. Most likely, as you get more involved with Silverlight, you will need to migrate to a paid version (Professional, Premium, Ultimate or Test). Although highly recommended, you do not need to use Visual Studio to develop Silverlight applications – the [free SDK](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=b3deb194-ca86-4fb6-a716-b67c2604a139&amp;displaylang=en) and command-line compilers are enough to build applications with. The open-source [MonoDevelop](http://monodevelop.com/) is also an option for developing Silverlight applications. It is worth noting that Visual Studio is only available for Windows. Mac developers have the tools to develop [Silverlight applications using Eclipse](http://www.eclipse4sl.org/) but this rarely happens.


#### SDKs and Toolkits

Unlike Flex, the [Silverlight SDK](http://www.microsoft.com/downloads/en/details.aspx?FamilyID=b3deb194-ca86-4fb6-a716-b67c2604a139&amp;displaylang=en) is not open-source but it is free. It includes the minimum tools necessary to build Silverlight applications. Very often, build servers and [CI](http://en.wikipedia.org/wiki/Continuous_integration) servers use the SDK alone, to avoid purchasing a license to Visual Studio on the server. Although the SDK is not open-source, the Silverlight Toolkit (produced by Microsoft) is. The [Silverlight Toolkit](http://silverlight.codeplex.com/) is an optional set of libraries that includes many add-ons such as rich controls, extended skinning and an in-browser test suite ([Silverlight Unit Tester](http://code.msdn.microsoft.com/silverlightut)).

#### Design

Visual Studio has an integrated graphical designer, but it is limited. Most people use [Expression Blend](http://www.microsoft.com/expression/products/Blend_Overview.aspx) to design, style and skin the UI. Expression Blend supports the import of assets from [Photoshop](http://www.adobe.com/products/photoshop/photoshop/) and [Illustrator](http://www.adobe.com/products/illustrator/). Expression Blend works on existing Visual Studio projects in parallel. This aids in a very tight integration between the designer and the developer. Flex cannot support this workflow very well. Even with the new version of [Catalyst](http://www.adobe.com/products/flashcatalyst/), projects are exported between the designer and developer – as opposed to the parallel workflow that Blend enables. Expression Blend only works in Windows.

#### Operating System Support

Silverlight does not have the same operating system penetration as Flex/Flash. Microsoft has fully committed to Windows and OSX for the browser and OOB capabilities. Silverlight does not, however, work in Linux. A separate project, dubbed [Moonlight](http://www.mono-project.com/Moonlight), aims to run Silverlight applications in Linux. This project is headed by the Mono team and it is perpetually behind the latest release of the Silverlight platform. Silverlight also runs as the primary application platform for the [Windows Phone 7](http://www.microsoft.com/windowsphone) and is being developed for [Symbian](http://www.symbian.org/), but it does not run on [Android](http://www.android.com/), [WebOS](http://developer.palm.com/), or [iOS](http://en.wikipedia.org/wiki/IOS_(Apple)) at this time. (possibly, in the future?)

#### Getting Started

The easiest way to get started with Silverlight is at [Silverlight.net](http://www.silverlight.net/getstarted/). From there, you can download all of the tools necessary to write applications in SIlverlight (Visual Web Developer, Expression Blend trial, Silverlight toolkit, etc)


### The Flex/Flash/Air Platform

In my opinion, the branding of Flex/Flash/Air can be confusing. I will attempt to make it simple. [Flash](http://www.adobe.com/products/flashplayer/) is the platform.&#160; [Flex](http://www.adobe.com/products/flex/) is the framework. [Air](http://www.adobe.com/products/air/) is Flex/Flash on the desktop (and devices outside the browser). In other words, Flash is the platform we all know and love. It has existed for a long time ([since at least 1996](http://en.wikipedia.org/wiki/Adobe_Flash)) and has a rich history. Flash, by itself, is not very good at writing Rich Internet Applications ([RIAs](http://en.wikipedia.org/wiki/Rich_Internet_application)) – it is very good at vector animation. Flex was introduced as an SDK to sit on top of Flash to enable application development. Air was introduced to allow Flex applications to run on the desktop.

If you were to compare the Adobe technology to [Silverlight](http://www.silverlight.net), Flex/Flash is to Silverlight as Air is to Silverlight Out-of-Browser.

#### Developer Story

Much like Silverlight, Flex employs a declarative UI mechanism ([MXML](http://en.wikipedia.org/wiki/MXML)) for defining the view of an application. Unlike Silverlight, Flex developers only have one language choice available to them – [ActionScript](http://en.wikipedia.org/wiki/ActionScript). ActionScript is an [ECMAScript](http://en.wikipedia.org/wiki/ECMAScript)-like language, similar to [Javascript](http://en.wikipedia.org/wiki/Javascript) but with optional [static typing](http://en.wikipedia.org/wiki/Static_typing#Static_typing), [classes](http://en.wikipedia.org/wiki/Class_(computer_science)) and [interfaces](http://en.wikipedia.org/wiki/Interface_(computer_science)). Developers can also enable legacy [C](http://en.wikipedia.org/wiki/C_(programming_language)) and [C++](http://en.wikipedia.org/wiki/C%2B%2B) code to run in the “ActionScript Virtual Machine” by using a currently in-development technology called [Adobe Alchemy](http://labs.adobe.com/technologies/alchemy/).

#### IDEs

Developers tend to use [Flash Builder](http://www.adobe.com/products/flashbuilder/) as the IDE for producing Flex applications. Unfortunately, there are not any free versions of Flash Builder (there is a free 60 day trial). Flex developers also have the option to use [IDEA Ultimate by JetBrains](http://www.jetbrains.com/idea/), which is also a paid product. Unlike SIlverlight, these IDEs work in WIndows, Mac or Linux. Like Silverlight, the SDK is free and does not require an IDE to develop with. Other options are [FlashDevelop](http://www.flashdevelop.org/) and [Tofino](http://www.ensemble.com/products/tofino.shtml) – a [Visual Studio](http://www.microsoft.com/visualstudio/en-us/visual-studio-2010-launch?WT.mc_id=SEARCH&amp;WT.srch=1) plug-in for Flex development.

#### SDKs and Toolkits

The Silverlight SDK is not open source. The [Flex SDK](http://www.adobe.com/products/flex/), however, is open source. It is a very simple zip-deployed download that can be copied anywhere you need. This makes integration with build and CI servers a cinch. By default, you use [Ant](http://en.wikipedia.org/wiki/Apache_Ant) for the build system, but any build manager. Similar to the [Silverlight Toolkit](http://silverlight.codeplex.com/), [flexlib](http://code.google.com/p/flexlib/) is a suite of value-added controls that you can bring into your solution.

#### Design

Since Flex is an Adobe product, the primary design environment is [Photoshop](http://www.adobe.com/products/photoshop/photoshop/) ([raster](http://en.wikipedia.org/wiki/Raster_graphics) art) and [Illustrator](http://www.adobe.com/products/illustrator/) ([vector](http://en.wikipedia.org/wiki/Vector_graphics) art). A third tool, [Catalyst](http://www.adobe.com/products/flashcatalyst/), is added as a bridge between the art tools and Flash Builder. Catalyst allows you to produce skins and styles using the Flex parts model. In the current release, the workflow is one way – Catalyst exports to a Flash Builder project. In future versions, Flash Builder will be capable of exporting back to Catalyst for a more bi-directional workflow. As far as I know, Catalyst does not support the parallel designer-developer workflow that [Expression Blend](http://www.microsoft.com/expression/products/Blend_Overview.aspx) does.&#160; 

#### Operating System Support

Flex has a rich set of operating system support. Flex already works in Flash (browser) on Windows, OSX, Linux and [Android](http://www.android.com/). In the near future, Air applications will also be deployable to [iOS](http://en.wikipedia.org/wiki/IOS_(Apple)), [Blackberry](http://na.blackberry.com/eng/playbook-tablet//), Android, [Windows Phone 7](http://www.microsoft.com/windowsphone) and [GoogleTV](http://www.google.com/tv/) in addition to Windows, OSX and Linux. The list is so exhaustive, I am probably missing some. In future versions of Flex, Adobe has strategies for managing different form factors. [Air 2.5](http://www.adobe.com/devnet/air/articles/air25_whatsnew.html) will include all of the APIs for mobile devices that you would expect (GPS, accelerometer, multi-touch, etc).

#### Getting Started

The easiest way to get started is to go to the [Flex product site](http://www.adobe.com/products/flex/) and download the 60 day [Flash Builder demo](http://www.adobe.com/products/flashbuilder/). Adobe also has demos of Catalyst, Photoshop and Illustrator.
 