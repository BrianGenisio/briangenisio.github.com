---
title: Binding to Resources in Silverlight/WPF
id: 61
categories:
  - Uncategorized
date: 2009-03-15 17:51:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fwww.houseofbilz.com%2Farchive%2F2009%2F03%2F15%2Fbinding-to-resources-in-silverlightwpf.aspx)](http://dotnetshoutout.com/Binding-to-Resources-in-SilverlightWPF) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f03%2f15%2fbinding-to-resources-in-silverlightwpf.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f03%2f15%2fbinding-to-resources-in-silverlightwpf.aspx)   

This article is describing how to solve the problem in SIlverlight, but the problem can be solved in WPF using the exact same technique.

So, now it is time to internationalize our application.&#160; I want to use the [built-in &quot;resx&quot; mechanism](http://msdn.microsoft.com/en-us/magazine/cc163609.aspx) for i18n in .net, so I create myself a resource file and start adding strings to the table:

[![image](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/BindingtoResourcesinSilverlightWPF_C384/image_thumb_1.png)](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/BindingtoResourcesinSilverlightWPF_C384/image_4.png) 

Visual Studio automatically generates the resource class for me, and I am ready to bind to the data in my XAML:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:f3950845-d6c0-4751-81df-2d232083ed5e" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">UserControl.Resources</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">l:i18n </span><span style="color: #FF0000;">x:Key</span><span style="color: #0000FF;">="i18n"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
</span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">UserControl.Resources</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">

</span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">StackPanel </span><span style="color: #FF0000;">x:Name</span><span style="color: #0000FF;">="LayoutRoot"</span><span style="color: #FF0000;"> Orientation</span><span style="color: #0000FF;">="Horizontal"</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBox </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">Button </span><span style="color: #FF0000;">Content</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Path=SearchButton, Source={StaticResource i18n}}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
</span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">StackPanel</span><span style="color: #0000FF;">&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

But I run my application and I get this exception: **Unhandled Error in Silverlight 2 Application AG_E_PARSER_BAD_TYPE**.&#160; I get this error message because the resx file is set to create my resource class as an &quot;internal&quot; class.&#160; Essentially, the SIlverlight engine doesn't have permission to instantiate a new i18n object, and it dies.

Fortunately, in Visual Studio 2008, they have given us a way to solve this problem.&#160; In the Resx editor, you can change the protection of the class it generates from internal to public.&#160; So, I do exactly that:

[![image](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/BindingtoResourcesinSilverlightWPF_C384/image_thumb_2.png)](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/BindingtoResourcesinSilverlightWPF_C384/image_6.png) 

Now my XAML should be able to bind to the resource, right?&#160; Wrong: **Unhandled Error in Silverlight 2 Application AG_E_PARSER_UNKNOWN_TYPE**.&#160; This time, it is complaining because the constructor to this public class that Visual Studio generated is internal!&#160; The class is public, but the constructor is internal, so the Silverlight/WPF engine still can't instantiate the object.

I searched the interwebs, and I'm not alone with this problem.&#160; Most people solve the problem by adding a custom build step that modifies the code.&#160; Some even suggest changing the constructor to public by hand every time you update the resource table.&#160; I am here to say: THERE IS A BETTER WAY!&#160; I just created a public class with a single property that exposes the class:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:d72a67d8-3810-4c9e-b2e7-9145f735e5fa" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> i18nPublic
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> i18n resources </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> i18n();
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> i18n Strings { </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> resources; } }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

By adding this class, I can make a slight modification to the resource type and the binding path and I am now binding directly to the resources.

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:8a94aeba-54bf-42d5-aa9a-4db30b09348f" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">UserControl.Resources</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
  </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">l:i18nPublic </span><span style="color: #FF0000;">x:Key</span><span style="color: #0000FF;">="i18n"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
</span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">UserControl.Resources</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">

</span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">StackPanel </span><span style="color: #FF0000;">x:Name</span><span style="color: #0000FF;">="LayoutRoot"</span><span style="color: #FF0000;"> Orientation</span><span style="color: #0000FF;">="Horizontal"</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBox </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">Button </span><span style="color: #FF0000;">Content</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Path=Strings.SearchButton, Source={StaticResource i18n}}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
</span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">StackPanel</span><span style="color: #0000FF;">&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>