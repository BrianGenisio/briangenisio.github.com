---
title: Introducing Html Utilities for Silverlight
id: 64
categories:
  - Uncategorized
date: 2009-04-26 17:54:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2009%2F04%2F26%2Fintroducing-html-utilities-for-silverlight.aspx)](http://dotnetshoutout.com/Introducing-Html-Utilities-for-Silverlight) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f04%2f26%2fintroducing-html-utilities-for-silverlight.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f04%2f26%2fintroducing-html-utilities-for-silverlight.aspx)   

I have just released an early version of [Html Utilties for Silverlight](http://htmlpageutil.codeplex.com/) on [CodePlex](http://www.codeplex.com/).&#160; This article will explain the whats and hows of this library.

### Who is this library for?

The primary target for this library is someone who:

1.  Develops software in Silverlight2.  Uses the Html DOM libraries in Silverlight  

### What problem does this library solve?

In the first version of this library, it is all about testability.&#160; Lets say I have a (very simple) method I want to test.&#160; The code looks like this:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:a895c8f8-9bbf-4fa0-9ab7-6e32c78a7965" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> HtmlGenerator
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> HtmlElement CreateImage(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> url)
    {
        var img </span><span style="color: #000000;">=</span><span style="color: #000000;"> HtmlPage.Document.CreateElement(</span><span style="color: #800000;">"</span><span style="color: #800000;">img</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        img.SetAttribute(</span><span style="color: #800000;">"</span><span style="color: #800000;">src</span><span style="color: #800000;">"</span><span style="color: #000000;">, url);
        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> img;
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Trying to write a test for this becomes very difficult.&#160; There are two problems here:

1.  HtmlPage is static, and testing CreateImage with this static is not a unit test.&#160; It relies on the browser.2.  Unless your tests are executing in the browser, there is no browser to fall back on.&#160; HtmlPage.Document fails with an exception that the HtmlPage is not enabled.

The test I WANT to write includes the ability to stub out the call to CreateElement and verify that the source was set.&#160; Using [NUnit](http://nunit.com/index.php) and [Moq](http://code.google.com/p/moq/), it would look something like this:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:1003e424-78dc-412b-ae49-292124deeee3" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #008000;">//</span><span style="color: #008000;"> JUST AN EXAMPLE OF WHAT I WANT TO DO.  SEE ACTUAL TEST FURTHER DOWN</span><span style="color: #008000;">
</span><span style="color: #000000;">[Test]
</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_Create_Image_Creates_Element_And_Sets_Src()
{
    var mockImage </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">HtmlElement</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();
    HtmlPage_Document.Setup(doc </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> doc.CreateElement(</span><span style="color: #800000;">"</span><span style="color: #800000;">img</span><span style="color: #800000;">"</span><span style="color: #000000;">))
        .Returns(mockImage.Object);

    var newImage </span><span style="color: #000000;">=</span><span style="color: #000000;"> TableGenerator.CreateImage(</span><span style="color: #800000;">"</span><span style="color: #800000;">AbcDef.png</span><span style="color: #800000;">"</span><span style="color: #000000;">);

    Assert.That(newImage, Is.EqualTo(mockImage.Object));
    mockImage.Verify(img </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> img.SetAttribute(</span><span style="color: #800000;">"</span><span style="color: #800000;">src</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">AbcDef.png</span><span style="color: #800000;">"</span><span style="color: #000000;">));
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

I can’t do anything like this because the HtmlPage is static and there are no interfaces on any of the classes in the DOM.

This is a very contrived example for the purpose of illustration, but I have had to use this approach in many cases from loading new windows, to inserting data into the DOM.

### What does this library do?

This library wraps all of the HtmlPage classes with wrappers that implement interfaces.&#160; For instance, HtmlPage is wrapped around a proxy that implements IHtmlPage.&#160; HtmlElement has a proxy wrapper that implements IHtmlElement.&#160; By using this approach, you can substitute anything you want in the Html DOM when you test.&#160; This may be any test object that implements the interfaces.&#160; I prefer to use Moq, but there are several other approaches to testing now that the system has interfaces to replace.

### How do I use the library?

In an attempt to make the migration as simple as possible, any code that uses HtmlPage.* is just replaced with Html.Page.* (notice the dot after Html).&#160; 

So, in the example above, the code would look like this now:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:d9020002-7d34-40c2-b26a-3a99999b715a" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> HtmlGenerator
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> IHtmlElement CreateImage(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> url)
    {
        var img </span><span style="color: #000000;">=</span><span style="color: #000000;"> Html.Page.Document.CreateElement(</span><span style="color: #800000;">"</span><span style="color: #800000;">img</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        img.SetAttribute(</span><span style="color: #800000;">"</span><span style="color: #800000;">src</span><span style="color: #800000;">"</span><span style="color: #000000;">, url);
        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> img;
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>
Notice that the only differences between these two methods are: 

1.  The return type is IHtmlElement instead of HtmlElement2.  HtmlPage.Document is replaced with Html.Page.Document

Next, my entire test fixture looks like this:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:b5799bc2-918a-4eec-a6e2-1aac3d657f45" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> HtmlGeneratorTests
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> MockHtml _mockHtml;

    [SetUp]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">virtual</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> SetUp()
    {
        _mockHtml </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> MockHtml();
    }

    [TearDown]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">virtual</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> TearDown()
    {
        _mockHtml.TearDown();
    }

    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_Create_Image_Creates_Element_And_Sets_Src()
    {
        var mockImage </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IHtmlElement</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();
        _mockHtml.Document.Setup(doc </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> doc.CreateElement(</span><span style="color: #800000;">"</span><span style="color: #800000;">img</span><span style="color: #800000;">"</span><span style="color: #000000;">))
            .Returns(mockImage.Object);

        var newImage </span><span style="color: #000000;">=</span><span style="color: #000000;"> HtmlGenerator.CreateImage(</span><span style="color: #800000;">"</span><span style="color: #800000;">AbcDef.png</span><span style="color: #800000;">"</span><span style="color: #000000;">);

        Assert.That(newImage, Is.EqualTo(mockImage.Object));
        mockImage.Verify(img </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> img.SetAttribute(</span><span style="color: #800000;">"</span><span style="color: #800000;">src</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">AbcDef.png</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

You may be noticing that this test references a class named MockHtml.&#160; This is a class that implements the HtmlPage system using Moq, but you can do this in any way.&#160; 

The most important thing to notice in this class is that we inject the IHtmlPage into the system using Html.SetHtmlPageForTesting() when we construct the object, and we set it back to null when we are complete.&#160; This is required so that when Html.Page is called, it calls your test implementation of IHtmlPage instead of the wrapper around the actual browser.

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:fdaa3ae5-4215-4454-8e0a-e62a69a0ec95" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> MockHtml
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IHtmlPage</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> Page { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IBrowserInformation</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> BrowserInformation { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IHtmlDocment</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> Document { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IHtmlWindow</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> Window { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> MockHtml()
    {
        Page </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IHtmlPage</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();
        BrowserInformation </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IBrowserInformation</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();
        Document </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IHtmlDocment</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();
        Window </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IHtmlWindow</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();

        Page.SetupGet(page </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> page.BrowserInformation).Returns(BrowserInformation.Object);
        Page.SetupGet(page </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> page.Document).Returns(Document.Object);
        Page.SetupGet(page </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> page.Window).Returns(Window.Object);

        Html.SetHtmlPageForTesting(Page.Object);
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> TearDown()
    {
        Html.SetHtmlPageForTesting(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">);
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### How do I get it?

Version 0.1 has been released on [CodePlex](http://htmlpageutil.codeplex.com/Release/ProjectReleases.aspx?ReleaseId=26667).&#160; The source code is available on this site, along with the release DLL.&#160; The Source project is broken into 4 projects: The HtmlUtilities project, a Silverlight test, a Web Site to host the Silverlight test, and a set of unit tests, showing how to mock out everything you would want to do with the library.

### Future plans

I have some thoughts for where this library should go.&#160; I want to aim towards a very easy to use API for manipulating the DOM through Silverlight.&#160; My original thought was that the roadmap might look something like:

0.1 -- Wrappers around existing objects 

0.2 -- Extensions to existing objects (HtmlLink, HtmlImage, HtmlDiv, etc) 

0.3 -- Helper utilities for doing more complex actions on the HTML 

0.4 -- Cross-browser issues abstracted away? 

0.5 -- Anything else?? 

1.0 -- World domination???

At least, in the next few versions, it would be nice to be able to do something like create a table using code that is much more clear than what it takes now.&#160; Possibly something that looks more like:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:c11076f5-8794-47d1-b174-e1907a12a9c6" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Table</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(</span><span style="color: #800000;">"</span><span style="color: #800000;">Border=1</span><span style="color: #800000;">"</span><span style="color: #000000;">,
    Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">THead</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(
        Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TR</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TH</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(</span><span style="color: #800000;">"</span><span style="color: #800000;">Header 1</span><span style="color: #800000;">"</span><span style="color: #000000;">), Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TH</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(</span><span style="color: #800000;">"</span><span style="color: #800000;">Header 2</span><span style="color: #800000;">"</span><span style="color: #000000;">)),
    Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TBody</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(
        Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TR</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TD</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(</span><span style="color: #800000;">"</span><span style="color: #800000;">Data 1.1</span><span style="color: #800000;">"</span><span style="color: #000000;">), Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TD</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(</span><span style="color: #800000;">"</span><span style="color: #800000;">Data 1.2</span><span style="color: #800000;">"</span><span style="color: #000000;">)),
        Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TR</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TD</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(</span><span style="color: #800000;">"</span><span style="color: #800000;">Data 2.1</span><span style="color: #800000;">"</span><span style="color: #000000;">), Html.New</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TD</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(</span><span style="color: #800000;">"</span><span style="color: #800000;">Data 2.2</span><span style="color: #800000;">"</span><span style="color: #000000;">)))));</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

The Table type would have static properties on it instead of the more awkward way of doing it now. I dunno... I'm just playing at this poing. Would that be useful?