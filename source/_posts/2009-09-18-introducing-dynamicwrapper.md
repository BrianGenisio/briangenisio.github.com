---
title: Introducing DynamicWrapper
id: 78
categories:
  - Uncategorized
date: 2009-09-18 13:50:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2009%2F09%2F18%2Fintroducing-dynamicwrapper.aspx)](http://dotnetshoutout.com/Introducing-DynamicWrapper) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f09%2f18%2fintroducing-dynamicwrapper.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f09%2f18%2fintroducing-dynamicwrapper.aspx)

[DynamicWrapper project on CodePlex](http://dynamicwrapper.codeplex.com/)

**_Edit: DynamicWrapper does not work in SIlverlight as I first though.&nbsp; My unit tests passed, but in runtime I get an exception when I try to use the class.&nbsp; I will be looking at some alternatives._**

Over the past few weeks, I have been working on a utility that I have wanted for years: something that allows me to apply an interface to an object that matches the contract but doesn’t actually implement the interface.&nbsp; In other words, I’ve wanted duck typing in C#.&nbsp; The code and project can be found on [CodePlex](http://www.codeplex.com/).&nbsp; It is set up so you simply copy one file into your solution and use the extension methods.

Why did I want this?&nbsp; Because I write a lot of unit tests and I develop in .Net with C#.&nbsp; Because I write way too many wrapper classes to make my code testable.&nbsp; For example, lets say that I have a class that I want to test and it acts upon a framework object:
 <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:68528c3e-ac9e-4d8f-994f-b84c31cb2d3d" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">sealed</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> FrameworkClass
{
    </span><span style="color: #0000FF;">internal</span><span style="color: #000000;"> FrameworkClass() {}

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> X {</span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">set</span><span style="color: #000000;">;}
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> Y {</span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">set</span><span style="color: #000000;">;}
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> Calculate() { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> DoSomeStuff(); }
}

</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> MyClass
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DoSomethingWithFrameworkData(FrameworkClass value)
    {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Do Something</span><span style="color: #008000;">
</span><span style="color: #000000;">    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div><pre class="c-sharp" name="code"></pre>

The problem is that I can’t test **MyClass** because it is dependent upon **FrameworkClass** which I can never construct myself (it is sealed with an internal constructor and no interface).&nbsp; The solution for this is simple but tedious – create a wrapper class that implements an interface and proxy through the wrapper class to the real object.&nbsp; This approach works, but I can say this: **_I am sick of writing wrapper classes!_**

This is why I created this DynamicWrapper utility.&nbsp; It exposes two extension methods: realObject.As&lt;Interface&gt;() and wrapper.AsReal&lt;ConcreteClass&gt;().&nbsp; It uses Reflection to emit a dynamically generated wrapper class that implements the interface, and wraps your object for you.&nbsp; It sounds complicated, but it is extremely simple to use.

Here is an example. Start by creating an interface that looks like the FrameworkClass:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:8bd836e1-3e63-4f29-8993-54b779d7971b" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">interface</span><span style="color: #000000;"> ICalculatable
{
    </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> X {</span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">set</span><span style="color: #000000;">;}
    </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> Y {</span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">set</span><span style="color: #000000;">;}
    </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> Calculate();
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Modify your class to depend on ICalculatable:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:1b0fecea-51fb-43ce-ae97-c5ff9b266994" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DoSomethingWIthFrameworkData(ICalculatable value) {}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Now, when you pass the framework class into the MyClass, you can wrap it with the interface:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:7863e811-abd6-4379-82ae-5dbcdd71ff96" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">myObject.DoSomethingWithFrameworkData(frameworkObject.As</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">ICalculatable</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">());</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

If you need the framework object to pass back to the framework, it is really simple:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:1f34493e-1fc7-472c-a06d-045b54e30ab4" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">wrapper.AsReal</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">FrameworkClass</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">()</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

That’s all there is to it!&nbsp; The utility is very simple.&nbsp; It just gets me out of the business of writing (and maintaining) wrapper classes. I am now free to get back to real development.

_*On a slightly related note, I will be focusing my learning efforts towards Ruby in the coming months.*_