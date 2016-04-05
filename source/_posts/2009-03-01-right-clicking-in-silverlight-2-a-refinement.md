---
title: Right-Clicking in Silverlight 2 -- A Refinement
id: 59
categories:
  - Uncategorized
date: 2009-03-01 17:49:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fwww.houseofbilz.com%2Farchive%2F2009%2F03%2F01%2Fright-clicking-in-silverlight-2----a-refinement.aspx)](http://dotnetshoutout.com/Right-Clicking-in-Silverlight-2-A-Refinement) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f03%2f01%2fright-clicking-in-silverlight-2----a-refinement.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f03%2f01%2fright-clicking-in-silverlight-2----a-refinement.aspx)   

### The Problem

If you search for &quot;[Right Click Silverlight 2](http://www.google.com/search?client=safari&amp;rls=en-us&amp;q=right+click+silverlight+2&amp;ie=UTF-8&amp;oe=UTF-8)&quot; in Google, you will get several hits.&#160; There are several ways to achieve this functionality, and they all have two things in common that I object to: 

1\. They use Javascript to send a message to the Silverlight application.&#160; While there is no way (that I know of) around this,&#160; requiring the hosting HTML to include this code is what I object to.&#160; Moving the app to a new page requires the same hookups that the developer may or may not remember to do.

2\. They send events around that require a hacky feel -- violating encapsulation and separations of concern (SOC) in a way that makes my skin itch.&#160;&#160; Any complex application with user controls strewn throughout will feel the pain of these approaches.

This article solves both of these problems:

First, don't require the user to write the Javascript every time.&#160; Instead let the Silverlight code execute the javascript that includes the necessary browser functionality. 

Second, instead of sending events around, or passing arguments through your user controls, use reflection to find the controls that care about right-clicks.&#160; This is the fun part.&#160; There exists a neat little static method called VisualTreeHelper.FindElementsInHostCoordinates().&#160; You pass it the mouse coordinates, and it returns a list of all of the controls that occupy space at that point.&#160; Then, just find any controls that implement the interface IRightClickable and call the method. 

### The Code
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:b0eaf7d2-f123-455a-aee0-cc8acd820a2e" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> RightClickHandler
{
   </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">interface</span><span style="color: #000000;"> IRightClickable
   {
       </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> OnRightClick(Point point);
   }

   </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> RightClickHandler()
   {}

   </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> RightClickHandler Instance </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> RightClickHandler();

   </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">bool</span><span style="color: #000000;"> Initialized { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }
   </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Initialize()
   {
       </span><span style="color: #0000FF;">if</span><span style="color: #000000;">(Initialized)
           </span><span style="color: #0000FF;">return</span><span style="color: #000000;">;

       HtmlPage.Window.Eval(
              </span><span style="color: #800000;">"</span><span style="color: #800000;">function OnClick(e) {</span><span style="color: #800000;">"</span><span style="color: #000000;"> </span><span style="color: #000000;">+</span><span style="color: #000000;">
              </span><span style="color: #800000;">"</span><span style="color: #800000;">    if (!e) e = window.event;</span><span style="color: #800000;">"</span><span style="color: #000000;"> </span><span style="color: #000000;">+</span><span style="color: #000000;">
              </span><span style="color: #800000;">"</span><span style="color: #800000;">    var silverlightControl = document.getElementById('</span><span style="color: #800000;">"</span><span style="color: #000000;"> </span><span style="color: #000000;">+</span><span style="color: #000000;"> HtmlPage.Plugin.Id </span><span style="color: #000000;">+</span><span style="color: #000000;"> </span><span style="color: #800000;">"</span><span style="color: #800000;">');</span><span style="color: #800000;">"</span><span style="color: #000000;"> </span><span style="color: #000000;">+</span><span style="color: #000000;">
              </span><span style="color: #800000;">"</span><span style="color: #800000;">    if (silverlightControl) silverlightControl.Content.RightClickListener.ProcessMouseEvent(e);</span><span style="color: #800000;">"</span><span style="color: #000000;"> </span><span style="color: #000000;">+</span><span style="color: #000000;">
              </span><span style="color: #800000;">"</span><span style="color: #800000;">}</span><span style="color: #800000;">"</span><span style="color: #000000;"> </span><span style="color: #000000;">+</span><span style="color: #000000;">
              </span><span style="color: #800000;">"</span><span style="color: #800000;">document.onmousedown = OnClick;</span><span style="color: #800000;">"</span><span style="color: #000000;"> </span><span style="color: #000000;">+</span><span style="color: #000000;">
              </span><span style="color: #800000;">"</span><span style="color: #800000;">document.oncontextmenu = function() { return false; }</span><span style="color: #800000;">"</span><span style="color: #000000;">);

       HtmlPage.RegisterScriptableObject(</span><span style="color: #800000;">"</span><span style="color: #800000;">RightClickListener</span><span style="color: #800000;">"</span><span style="color: #000000;">, Instance);

       Initialized </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">true</span><span style="color: #000000;">;
   }

   [ScriptableMember]
   </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> ProcessMouseEvent(ScriptObject evt)
   {
       </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> button </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">int</span><span style="color: #000000;">.Parse(evt.GetProperty(</span><span style="color: #800000;">"</span><span style="color: #800000;">button</span><span style="color: #800000;">"</span><span style="color: #000000;">).ToString());
       </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (button </span><span style="color: #000000;">==</span><span style="color: #000000;"> </span><span style="color: #800080;">2</span><span style="color: #000000;">)
       {
           var location </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Point
           {
               X </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">double</span><span style="color: #000000;">.Parse(evt.GetProperty(</span><span style="color: #800000;">"</span><span style="color: #800000;">clientX</span><span style="color: #800000;">"</span><span style="color: #000000;">).ToString()),
               Y </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">double</span><span style="color: #000000;">.Parse(evt.GetProperty(</span><span style="color: #800000;">"</span><span style="color: #800000;">clientY</span><span style="color: #800000;">"</span><span style="color: #000000;">).ToString())
           };

           </span><span style="color: #0000FF;">foreach</span><span style="color: #000000;"> (IRightClickable element </span><span style="color: #0000FF;">in</span><span style="color: #000000;"> FindElements</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IRightClickable</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(location))
               element.OnRightClick(location);
       }
   }

   </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> IEnumerable</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">T</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> FindElements</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">pT</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(Point absoluteLocation) </span><span style="color: #0000FF;">where</span><span style="color: #000000;"> T : </span><span style="color: #0000FF;">class</span><span style="color: #000000;">
   {
       </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> from element </span><span style="color: #0000FF;">in</span><span style="color: #000000;"> VisualTreeHelper.FindElementsInHostCoordinates(absoluteLocation, Application.Current.RootVisual)
              </span><span style="color: #0000FF;">where</span><span style="color: #000000;"> element </span><span style="color: #0000FF;">is</span><span style="color: #000000;"> T
              select element </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> T;
   }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### The Usage

Simply initialize the RightClickHandler code somewhere (It can be called more than once without any ill effects), and implement the interface.&#160; If the control is right-clicked, OnRightClick() will be called! 

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:49e0ed9e-fdc5-447f-8bc1-4f2b89924a4c" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">partial</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> TestControl : UserControl, RightClickHandler.IRightClickable
{
   </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> TestControl()
   {
       RightClickHandler.Initialize();
       InitializeComponent();
   }

   </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> OnRightClick(Point point)
   {
       Input.Text </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #800000;">"</span><span style="color: #800000;">Clicked: (</span><span style="color: #800000;">"</span><span style="color: #000000;"> </span><span style="color: #000000;">+</span><span style="color: #000000;"> point.X </span><span style="color: #000000;">+</span><span style="color: #000000;"> </span><span style="color: #800000;">"</span><span style="color: #800000;">, </span><span style="color: #800000;">"</span><span style="color: #000000;"> </span><span style="color: #000000;">+</span><span style="color: #000000;"> point.Y </span><span style="color: #000000;">+</span><span style="color: #000000;"> </span><span style="color: #800000;">"</span><span style="color: #800000;">)</span><span style="color: #800000;">"</span><span style="color: #000000;">;
   }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### Windowless Mode 

It seems that there is no way around this.&#160; The Silverlight plugin must be created as &quot;Windowless&quot; in order to receive right clicks.&#160; I would have loved to encapsulate this in the RightClickHandler class, but it seems that there is no way.&#160; In your aspx, you would have: 

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:b244a652-97a8-4932-ae57-de1cbd265e43" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">asp:Silverlight </span><span style="color: #FF0000;">Windowless</span><span style="color: #0000FF;">="true"</span><span style="color: #FF0000;"> runat</span><span style="color: #0000FF;">="server"</span><span style="color: #FF0000;"> ... </span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

If you are using straight HTML, you would add this parameter: 

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:37308b64-99a2-45c6-a9d2-94d1ee27e2b9" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">param </span><span style="color: #FF0000;">name</span><span style="color: #0000FF;">="windowless"</span><span style="color: #FF0000;"> value</span><span style="color: #0000FF;">="true"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>