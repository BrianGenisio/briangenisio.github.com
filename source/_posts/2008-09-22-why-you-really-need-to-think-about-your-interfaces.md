---
title: Why you REALLY need to think about your interfaces
id: 30
categories:
  - Uncategorized
date: 2008-09-22 12:29:00
tags:
---

[![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fbrian.genisio.org%2f2008%2f09%2fwhy-you-really-need-to-think-about-your.html)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fbrian.genisio.org%2f2008%2f09%2fwhy-you-really-need-to-think-about-your.html)   

I was working on performance tuning an application the other day.&#160; The code in this post is a rough approximation of the problem I had to solve.&#160; The application defines a plug-in interface that the plug-in developers can implement.&#160; The plug-in gets initialized with a dictionary and and the application talks through the rest of the interface.

   <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:d6b6dda9-3e31-4ffc-a589-076ad08d52be" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">interface</span><span style="color: #000000;"> IPlugin   
{        
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Initialize(Dictionary</span><span style="color: #000000;">&lt;</span><span style="color: #0000FF;">string</span><span style="color: #000000;">, Information</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> dataLibrary);
    </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> TalkToMe(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message);
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

The initialization from the application is simple:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:0e1639c0-a9ef-43ac-9ef7-0d4cf68082ae" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> SetUpPlugin(IPlugin plugin)   
{
    Dictionary</span><span style="color: #000000;">&lt;</span><span style="color: #0000FF;">string</span><span style="color: #000000;">, Information</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> data </span><span style="color: #000000;">=</span><span style="color: #000000;"> PopulateDictionary();
    plugin.Initialize(data);    
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

When this interface was originally written, the dictionary was populated using XML files.&#160; The dictionary only included about two hundred items, but over time, this dictionary became more complex.&#160; It grew out of the XML files, and into a database.&#160; It grew into tens of thousands of items.&#160; It is not hard to tell that there is a performance bottleneck here as the application populates this many items from the database.&#160; It turns out that the plug-ins don't tend to need a lot of the data in the dictionary, though I cannot predict what it will need to lookup.&#160; To populate the entire dictionary takes a TON of time, and it is completely unnecessary.

Unfortunately, I am bound to this interface, which is already published to our plug-in developers.&#160; My first inclination was to write a custom Dictionary&lt;&gt; class.&#160; It would be really nice if .NET would let me do something like this:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:f513dde0-453a-4a29-80f3-e5c3e02ee1e0" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> DataLibrary : Dictionary</span><span style="color: #000000;">&lt;</span><span style="color: #0000FF;">string</span><span style="color: #000000;">, Information</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">   
{    
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> IApplicationLogic _logic;    
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> DataLibrary(IApplicationLogic logic)    
    {        
        _logic </span><span style="color: #000000;">=</span><span style="color: #000000;"> logic;    
    }     

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">override</span><span style="color: #000000;"> Information </span><span style="color: #0000FF;">this</span><span style="color: #000000;">[</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> key]    
    {        
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;">        
        {            
            </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> _logic.LookupInformation(key);        
        }    
    }    

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">override</span><span style="color: #000000;"> </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> Count    
    {        
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;">        
        {            
            </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> _logic.InformationCount;        
        }    
    }    
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> etc</span><span style="color: #008000;">
</span><span style="color: #000000;">}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

This would solve my needs completely if only methods were not sealed by default in C#.&#160; Unfortunately, they are, and .NET didn't make the Dictionary members virtual.&#160; In fact, it turns out, I am completely coded into a corner since I can't change the interface.&#160; If only the IPlugin interface were thought through a bit further, I wouldn't be in this predicament.&#160; 

All that really needed to happen was for IPlugin.Initialize to take an IDictionary&lt;string, Information&gt; instead of a Dictionary&lt;string, Information&gt; (notice the 'I' in front of the Dictionary, indicating that it is an interface as opposed to a concrete class.)&#160; If this were only the case, I would be able to implement the interface using a database lookup and move on with my life.

Now, I really don't have any solution other than asking for an interface change to all of the plug-in writers for my application.&#160; This not only looks bad, but it IS bad.&#160; I suppose might be able to come up with something crazy using Dynamic Proxies, but that would get rather ugly rather quickly.

The more you think about it, the choice of Dictionary&lt;string, Information&gt; was bad for other reasons as well.&#160; The Dictionary class is a read/write class.&#160; We do not want our plug-in authors to modify the dictionary -- we only want them to look up data.&#160; We don't need them to iterate over the data, and we don't need to give them a vectored view of the keys or the values.

The message I am trying to get through here is simple:&#160; When you are creating an interface for public use, you need to think hard about what that interface looks like.&#160; Avoid concrete data passing when interfaces are available (IDictionary instead of Dictionary).&#160; These interfaces don't get to change much.&#160; They aren't as flexible as the internal code.&#160; Please, think about your interfaces.