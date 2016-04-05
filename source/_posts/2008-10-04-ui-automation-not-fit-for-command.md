---
title: 'UI Automation Not Fit for Command!'
id: 34
categories:
  - Uncategorized
date: 2008-10-04 12:39:00
tags:
---

[![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fbrian.genisio.org%2f2008%2f10%2fui-automation-not-fit-for-command.html)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fbrian.genisio.org%2f2008%2f10%2fui-automation-not-fit-for-command.html)   
I have been spending a lot of time exploring automation testing frameworks that can be driven using NUnit. I am exploring the following:   

*   Roll Your Own*   [NUnit Forms](http://nunitforms.sourceforge.net/)*   [Ranorex](http://www.ranorex.net/)*   [Microsoft UI Automation](http://msdn.microsoft.com/en-us/library/ms747327.aspx) I am finding that the &quot;Roll Your Own&quot; method is the most versatile method. I will write about this method later. Today, I feel like a more negative tone. I will say it now:   

<span style="font-weight: bold">Microsoft's UI Automation Framework is NOT fit for command.</span>   

Don't get me wrong... there are a lot of easy-to-use systems in .NET. BUT, I find the Code DOM easier to use than the UI Automation tools. If you have ever used the Code DOM, you know that I am saying a lot with this.   

Here are my issues with the UI Automation Framework:   

*   UI Spy Doesn't Ship with Visual Studio 2008\. If you want UI Spy, you need to download the[ SDK for Windows Vista](http://www.microsoft.com/downloads/details.aspx?familyid=4377F86D-C913-4B5C-B87E-EF72E5B4E065&amp;displaylang=en). Don't try to use this framework without it. You will fail.*   The UI Spy tool is buggy and crashy and slow. Did I mention that this tool is essential?*   As much as I research it, I can't figure out how to do the first two things I tried to do: Click on a LinkLabel and enter text into a RichTextBox. I know how to click on a button and enter text into a TextBox, but the more complicated versions of this are too difficult, aparently.*   The API is terribly difficult to write AND read. Not intuitive at all!*   Finding controls using the NameProperty doesn't use the &quot;Name&quot; property. It uses the &quot;Text&quot; property. If you want to search for a control using the &quot;Name&quot; property, you need to use AutomationIdProperty. Did I mention that the API is awful? This is why you need the UI Spy tool.*   The framework itself is slow*   You need a separate thread to catch modal dialogs. Have you ever tried to write tests that utilize multiple threads? These are the problems I have run into with just a few hours of use. I need to mention that NUnit Forms, Ranorex and &quot;Roll Your Own&quot; tests were all easier to write/read/execute with the same level of effort.  