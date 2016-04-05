---
title: Drag and Drop with Silverlight
id: 56
categories:
  - Uncategorized
date: 2009-02-10 17:45:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2009%2F02%2F10%2Fdrag-and-drop-with-silverlight.aspx)](http://dotnetshoutout.com/Drag-and-Drop-with-Silverlight) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f02%2f10%2fdrag-and-drop-with-silverlight.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f02%2f10%2fdrag-and-drop-with-silverlight.aspx)   

### The Problem

I have been developing with Silverlight for a few months now, and I have really been enjoying myself.&#160; It has been the enabling technology my project and we have been extremely productive in the environment.&#160; Unfortunately, Silverlight is still in version 2.0 and there are some missing capabilities.

One such hole in the framework is &quot;Drag and Drop&quot;.&#160; There is no support for it directly.&#160; There are several blog examples on the web, but I have yet to find a fully encapsulated, generic solution to the &quot;Drag and Drop&quot; problem.&#160; For instance, how soon after dragging do you really want to be dragging?&#160; You don't want to drag immediately, as that will affect normal clicking on an element.&#160; How do you handle drags from one distinct control to another that are not aware of each other?&#160; How do you clue the user in that a given control is droppable?&#160; How do you add animation to cue the user that the item is being dropped?

### Introducing DragNDrop

To answer these questions, I created the **DragNDrop** class.&#160; It is a manager of sorts, and watches a &quot;Drag Source&quot; for the mouse down events.&#160; The &quot;Drag Source&quot; implements an interface and the &quot;Drop Spot&quot; implements a complimentary interface.&#160; This allows for the &quot;Drag Source&quot; and &quot;Drop Spot&quot; to be blissfully unaware of each other.

### Usage

The assumption made with this class is that there is some sort of a payload.&#160; This payload is picked up from the &quot;Drag Source&quot; and dropped into the &quot;Drop Spot&quot;.&#160; The &quot;Drop Spot&quot; can accept a payload of any types that it implements from any &quot;Drag Source&quot; that implements the complementary interface.&#160; Lets take a look at the interfaces:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:0b342a59-bca7-4daa-bfe0-7e2312fca29b" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> DragNDrop</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">PayloadType</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">interface</span><span style="color: #000000;"> IThumbnailDragSource
    {
        FrameworkElement DragCursor { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; }
        PayloadType Payload { </span><span style="color: #0000FF;">get</span><span style="color: #000000;">; }
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">interface</span><span style="color: #000000;"> IThumbnailDropSpot
    {
        </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DragDropEnter();
        </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DragDropExit();  
        </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> ThumbnailDroping(PayloadType dataContext, FrameworkElement cursor, Point cursorPosition);
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

For instance, if a &quot;Drag Source&quot; wanted to allow dragging with a string payload, it would implement **DragNDrop&lt;string&gt;.IThumbnailDragSource**.&#160; Any &quot;Drop Spot&quot; that wants to accept a string payload would implement **DragNDrop&lt;string&gt;.IThumbnailDropSpot**.&#160; The **DragNDrop** class is then constructed with an instance of the &quot;Drag Source&quot; and the **DragNDrop** class handles everything else.

In addition to the payload, the &quot;Drag Source&quot; needs to provide the **DragNDrop** class with the cursor that will be displayed and dragged across the screen.&#160; This can be anything; an image, a user control, a rectangle, etc.

The &quot;Drop Spot&quot; will be notified when the cursor is entering and exiting its space, so it can react appropriately.&#160; It will also be notified when the payload is dropped with the instance of the cursor and the absolute position.&#160; This is necessary in case the &quot;Drop Spot&quot; wants to animate the drop in any way.

### The Demo

I created the [Twitter Search Tool](http://brianstestsite.googlepages.com/DragAndDrop.html) to prove out this concept.&#160; The idea is that search for a term and it returns a list of tweets. When you find a tweet that you like, you can drop it on to the second list to keep track of it.&#160; It is not the most functional app in the world, but it illustrates the usage of the **DragNDrop** class nicely.&#160; Take a moment to try it out.

Now that you have seen it in action, lets take a look at how I use the **DragNDrop** class.&#160; This application uses a small data structure called **Tweet**.&#160; It contains all of the information about a given tweet (user name, user image, text, etc).&#160; The **Tweet** is the payload.

In the Silverlight UI, I created a user control to represent each item in the list called **ListItem**.&#160; The XAML is available in the full source if you are curious.

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:28ee7663-19ed-43fd-9701-c0b058d3c912" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">partial</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> ListItem : UserControl, DragNDrop</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Tweet</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">.IThumbnailDragSource
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> DragNDrop</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Tweet</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> _dragNDrop;

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ListItem()
    {
        InitializeComponent();

        _dragNDrop </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> DragNDrop</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Tweet</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(</span><span style="color: #0000FF;">this</span><span style="color: #000000;">);
        _dragNDrop.DraggingEnabledDistance </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #800080;">5.0</span><span style="color: #000000;">;
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> FrameworkElement DragCursor
    {
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;">
        {
            </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Image
            {
                Width </span><span style="color: #000000;">=</span><span style="color: #000000;"> TweetImage.ActualWidth,
                Height </span><span style="color: #000000;">=</span><span style="color: #000000;"> TweetImage.ActualHeight,
                Source </span><span style="color: #000000;">=</span><span style="color: #000000;"> TweetImage.Source,
                Opacity </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #800080;">0.5</span><span style="color: #000000;">
            };
        }
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> Tweet Payload
    {
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> DataContext </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> Tweet; }
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

This code is pretty straight-forward.&#160; The DragCursor returns a new image with the picture of the user.&#160; The payload is the actual Tweet.&#160; The constructor creates an instance of **DragNDrop** and passes itself in as the source.&#160; It also sets the **DraggingEnabledDistance**.&#160; This is the distance (in pixels) that the user must drag before the dragging really begins.&#160; The default is 10 pixels.

Next, lets take a look at the &quot;Drop Spot&quot; code.&#160; It is also a user control, **TweetDropList,** which includes a ListBox control.&#160; It also includes an opaque canvas that gives the effect of highlighting when it is made visible.

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:dff8e087-a3c4-46f8-b573-f73c39305ea8" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">partial</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> TweetDropList : DragNDrop</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Tweet</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">.IThumbnailDropSpot
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> ObservableCollection</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Tweet</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> _savedTweets </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> ObservableCollection</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">Tweet</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> TweetDropList()
    {
        InitializeComponent();
        DropList.ItemsSource </span><span style="color: #000000;">=</span><span style="color: #000000;"> _savedTweets;
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DragDropEnter()
    {
        Highlight.Visibility </span><span style="color: #000000;">=</span><span style="color: #000000;"> Visibility.Visible;
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DragDropExit()
    {
        Highlight.Visibility </span><span style="color: #000000;">=</span><span style="color: #000000;"> Visibility.Collapsed;
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> ThumbnailDroping(Tweet dataContext, FrameworkElement cursor, Point cursorPosition)
    {
        DragDropExit();
        AnimateCursor(cursor, cursorPosition, () </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> _savedTweets.Add(dataContext));
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

I have left out the **AnimateCursor** code, but it simply generates a storyboard on the cursor and quickly morphs it down to a size of zero.&#160; That code can be found in the source bundle.&#160; The rest of this implementation is extremely simple.&#160; **DragDropEnter** and **DragDropExit** simply hides and shows the highlight layer.&#160; **ThumbnailDropping** will remove the highlighting layer and animate the cursor to give the effect of the item being dropped.&#160; When the animation completes, the new tweet is added to the **_savedTweets** collection, which will cause the** **user control to display the new tweet.

### Thats It!

I designed the **DragNDrop** class to be as simple to use as possible.&#160; I have been using a version of this class in my product and I have had great results.&#160; The &quot;Drop Spots&quot; can implement as many versions of **IThumbnailDropSpot** as it needs to allow different types of items to be dragged.&#160; The **DragNDrop** class can be instantiated as many times as necessary, for every &quot;Drag Source&quot;.&#160; 

Please play with this class and give me feedback.&#160; I'd love to hear how it is being used.

[DragNDrop Twitter Demo](http://brianstestsite.googlepages.com/DragAndDrop.html) 

[DragNDrop Twitter Demo Source](http://brianstestsite.googlepages.com/DragNDrop_Source.zip) 

[DragNDrop.cs](http://brianstestsite.googlepages.com/DragNDrop.cs) 

[Extensions.cs](http://brianstestsite.googlepages.com/Extensisons.cs) (a handful of extension methods that DragNDrop uses)