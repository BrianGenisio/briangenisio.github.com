---
title: 'Re-Thinking C# Events'
id: 58
categories:
  - Uncategorized
date: 2009-02-15 17:47:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2009%2F02%2F15%2Fre-thinking-c-events.aspx) ](http://dotnetshoutout.com/Re-Thinking-C-Events)[![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f02%2f15%2fre-thinking-c-events.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f02%2f15%2fre-thinking-c-events.aspx)   

Back when I was learning C#, I was taught a pattern for events that went something like this:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:c8b7f72c-ad98-491f-a770-94a820ed6b54" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> Tribe
{
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> For demonstration only.  Please do not write code like this.</span><span style="color: #008000;">
</span><span style="color: #000000;">    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> TribesmanAddedEventArgs : EventArgs
    {
        </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> Tribesman _tribesman;
        </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> TribesmanAddedEventArgs(Tribesman tribesman)
        {
            _tribesman </span><span style="color: #000000;">=</span><span style="color: #000000;"> tribesman;
        }

        </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> Tribesman NewTribesman
        {
            </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> _tribesman; }
        }
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">delegate</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> TribesmanAddedDelegate(</span><span style="color: #0000FF;">object</span><span style="color: #000000;"> sender, TribesmanAddedEventArgs args);

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">event</span><span style="color: #000000;"> TribesmanAddedDelegate TribesmanAdded;

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> OnTribesmanAdded(Tribesman tribesman)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;">(TribesmanAdded </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
            TribesmanAdded(</span><span style="color: #0000FF;">this</span><span style="color: #000000;">, </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> TribesmanAddedEventArgs(tribesman));
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Needless to say, this is a pretty awful pattern.&#160; Add a few more events to this class (TribesmanRemoved, TribesmanModified, etc) and your code becomes a complete mess really quickly.

With some of the advances that C# language has made, and the EventHandler&lt;&gt; generic delegate, we can thankfully clean this up a bit:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:159ca3fe-e3f3-4f51-8bbb-13ecd5a1fa65" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> Tribe
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> TribesmanAddedEventArgs : EventArgs
    {
        </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> TribesmanAddedEventArgs(Tribesman tribesman)
        {
            NewTribesman </span><span style="color: #000000;">=</span><span style="color: #000000;"> tribesman;
        }

        </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> Tribesman NewTribesman { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">event</span><span style="color: #000000;"> EventHandler</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">TribesmanAddedEventArgs</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> TribesmanAdded;

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> OnTribesmanAdded(Tribesman tribesman)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;">(TribesmanAdded </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
            TribesmanAdded(</span><span style="color: #0000FF;">this</span><span style="color: #000000;">, </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> TribesmanAddedEventArgs(tribesman));
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Can we take this any further? You bet. The EventArgs classes tend to be very boilerplate. Lets generalize it so we only have to write this code once:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:ea8133ff-6ba4-4416-a516-08a25688e2e5" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> EventArgs</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">PayloadType</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> : EventArgs
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> EventArgs(PayloadType payload)
    {
        Payload </span><span style="color: #000000;">=</span><span style="color: #000000;"> payload;
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> PayloadType Payload { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">set</span><span style="color: #000000;">; }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Now that we have that out of the way, the event pattern can be drastically reduced:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:b4bfcdfb-33ef-4077-8992-2261a961c404" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> Tribe
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">event</span><span style="color: #000000;"> EventHandler</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">EventArgs</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Tribesman</span><span style="color: #000000;">&gt;&gt;</span><span style="color: #000000;"> TribesmanAdded;

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> OnTribesmanAdded(Tribesman tribesman)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;">(TribesmanAdded </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
            TribesmanAdded(</span><span style="color: #0000FF;">this</span><span style="color: #000000;">, </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> EventArgs</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Tribesman</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(tribesman));
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

This code is really starting to look better.&#160; Next, through the magic of extension methods, we can eliminate the &quot;OnTribesmanAdded&quot; method as well:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:9e12f09e-39d2-4434-a665-5c075bc5a46b" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> EventExtensions
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Fire</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">T</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(</span><span style="color: #0000FF;">this</span><span style="color: #000000;"> EventHandler</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">EventArgs</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">T</span><span style="color: #000000;">&gt;&gt;</span><span style="color: #000000;"> handler, </span><span style="color: #0000FF;">object</span><span style="color: #000000;"> sender, T payload)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;">(handler </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
            handler(sender, </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> EventArgs</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">T</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(payload));
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

There we go!&#160; Now, anywhere in the class that you want to fire the event, you can just call TribesmanAdded.Fire(this, newTribesman) and not worry if the event has been subscribed to. This is a case where you can call a method on a null object safely, because &quot;Fire&quot; is actually a static method.&#160; Even better, the event code in our class can be reduced to this:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:36a5a49d-1c32-48f1-b96e-5e8648c57607" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> Tribe
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">event</span><span style="color: #000000;"> EventHandler</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">EventArgs</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Tribesman</span><span style="color: #000000;">&gt;&gt;</span><span style="color: #000000;"> TribesmanAdded;
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

In my opinion, this is the way we should be writing events in C# 3.0 and beyond.&#160; What do you think?