---
title: Disabling Navigation Flicks in WPF
tags:
  - Navigation
  - WPF
id: 102
categories:
  - Uncategorized
date: 2010-06-02 12:32:00
---

I am currently working on a multi-touch application using WPF.&nbsp; One thing that has been irritating me with this development is an automatic navigation forward/back command that is bound to forward and backwards flicks.&nbsp; Many of my touch-based interactions were being thwarted by gestures picked up by WPF as navigation.&nbsp; I just wanted to disable this behavior. 

My programmatic back/forward calls are not affected by this change, which is nice.&nbsp; Here is how I did it:&nbsp; In my main window, I added the following command bindings:
 <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:27adb24f-bf8c-4cad-a097-4e4bc315e86e" class="wlWriterEditableSmartContent"><pre style="background-color:White;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">NavigationWindow.CommandBindings</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">CommandBinding </span><span style="color: #FF0000;">Command</span><span style="color: #0000FF;">="NavigationCommands.BrowseBack"</span><span style="color: #FF0000;"> Executed</span><span style="color: #0000FF;">="DoNothing"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">CommandBinding </span><span style="color: #FF0000;">Command</span><span style="color: #0000FF;">="NavigationCommands.BrowseForward"</span><span style="color: #FF0000;"> Executed</span><span style="color: #0000FF;">="DoNothing"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
</span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">NavigationWindow.CommandBindings</span><span style="color: #0000FF;">&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>
<style type="text/css">csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }
</style>

Then, the **DoNothing **method in the code-behind does nothing:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:633c89eb-9164-493e-8dc2-ae730bc14a53" class="wlWriterEditableSmartContent"><pre style="background-color:#FFFFFF;overflow: auto;"><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DoNothing(</span><span style="color: #0000FF;">object</span><span style="color: #000000;"> sender, ExecutedRoutedEventArgs e) { }</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>
<style type="text/css">csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }
</style>
<pre class="csharpcode"></pre>
<style type="text/css">csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }
</style>

There may be a better way to do this, but I haven’t found one.