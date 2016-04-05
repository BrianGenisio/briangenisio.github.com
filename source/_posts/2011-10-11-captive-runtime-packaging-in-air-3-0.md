---
title: Captive Runtime packaging in Air 3.0
date: 2011-10-11 17:42:09
layout: post
category: Software
tags: [Development, Flash Builder, Flex]
permalink: archives/2011/10/11/captive-runtime-packaging-in-air-3-0/
---

When I talk with people about the Flex framework, many think of it as a Flash library. They often refer to Flex as a web framework that runs in a plug-in. To me, however, Flex is an application framework. To be honest, Flex in the browser doesn’t excite me. I am much more interested in using Flex on the desktop or on mobile devices.

**Some History**

Until recently, Flex has been criticized for requiring a separate runtime install. The criticism is valid – users were often confused by this and found the need to upgrade something other than the app they wanted to install. The user experience became one where the user was left wondering, “what just happened?”

In one case, my customer required that nothing be allowed installed on the users’ machine (network access was not available either). This meant no Air runtime and no app. We had a workaround which included an app embedded inside of a PDF but that left the user feeling like they were watching Inception (an app within an app…).

Although this was the case in OSX, Windows and Android, it was not the case in iOS (iPhone and iPad). Apple doesn’t allow third-party runtimes on their devices so Adobe created a native compiler and packager that bundles the Air libraries directly into the app.

**Captive Runtime**

In Air 3.0, however, the requirement for a separate Air runtime has been lifted. With the “Captive Runtime” feature, developers can bundle the native Air libraries with the app. When apps are bundled this way, they can be deployed any way you want. They won’t require an installation of Air OR the application itself (on the desktop; mobile apps still need to be installed but can be without the separate runtime). This doesn’t mean you are restricted from installing your app but it means that it is no longer required. In addition to iOS, Captive Runtime will work in Android, OSX and Windows.

Let’s see how easy it is to use. I will create a “Hello World” app for illustrative purposes but significantly more complicated applications work just as easily.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/29619d66-af32-458e-b4d6-dc43166cc114/2011-10-06_11-42-17.png)

[Flash Builder 4.6](http://www.adobe.com/devnet/flex/articles/whats-new-flex-flash-builder-46.html) has integration built-in integration for captive runtime.  When you “Export Release Build” from Flash Builder, you simply create a “Signed application with AIR runtime bundled”.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/945de260-d491-4a2a-91c1-74ab7cb7c9b3/2011-10-06_06-55-542.png)

Everything else is the same. You add a signed certificate to the app as you normally would and any other packaging settings.  When you are complete, you will get a native application that is ready to execute but doesn’t require Air to be installed.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/202db853-961d-47a9-8388-b34332892e19/2011-10-06_10-41-36.png)

This app can be copied to a thumb drive, shared from network storage or embedded into an installer. It is an application just like any other.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/46a53a46-6b33-4523-a20e-db09d071eda0/2011-10-06_10-44-36.png)

Note that this does bloat the size of your application. This may or may not be an issue for you depending on your audience and deployment mechanism. You can always package your app as a traditional Air app if you prefer.

The Windows story for building your app is exactly the same as the OSX side. The only difference is that the app is packaged as an EXE with the Air support alongside it.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/41811197-7d4d-4fcd-9bad-af15d4a39ea6/2011-10-06_11-30-16.png)

The story for Android is similar. You have a different dialog for packaging, but the idea is the same. The APK that is produced will have Air bundled inside.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/4b0f8c73-28ea-4118-8012-021863e57edc/2011-10-06_11-23-11.png)

Of course, you don’t need to use Flash Builder to package your app. You can do it from the SDK and the command line. Instead of repeating what you can already find out in other places, I will just refer you to [Andrew Trice’s blog](http://www.tricedesigns.com/2011/08/10/air-3-0-captive-runtime/) for more info on that.

&nbsp;

If you would like to get a sneak peek of Flash Builder 4.6, you can [apply for the Flex and Flash Builder pre-release](https://prerelease.adobe.com/callout/default.html?callid=DC919522A42544798C33ECB4041FC5DC).

&nbsp;