---
title: Have you seen the new ANTS profiler?
id: 29
categories:
  - Uncategorized
date: 2008-09-12 12:28:00
tags:
---

[![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fbrian.genisio.org%2f2008%2f08%2fhave-you-seen-new-ants-profiler.html)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fbrian.genisio.org%2f2008%2f08%2fhave-you-seen-new-ants-profiler.html)   
Before I write anything, I want to make it clear: I am not a Red-Gate shill. They have not paid me to say this... in fact, my team pays them for their software. I just feel that sometimes, you just need to recognize superior work.   

<font color="#ff0000">Edit: Since writing this post, Red-Gate has sent me a [SWAG](http://www.urbandictionary.com/define.php?term=swag) package with a T-Shirt, coffee mug and 1 GB USB drive. They did this to thank me for the [testimonial](http://www.red-gate.com/products/ants_profiler/testimonials.htm) of mine that they published. Again, this happened AFTER this post, and I am still not a Red-Gate shill.</font>   

<span style="font-weight: bold">ANTS of the Past</span>   
My team has been using the [.Net ANTS Profiler](http://www.red-gate.com/products/ants_profiler/index.htm) for years. It has always been a useful tool in our arsenal for finding performance bottlenecks. In the past, I have always liked the product, but have never felt like it was anything extremely special. One of the biggest reasons for this is the often mis-represented hot spots.   

I often found that methods that got called many times (hundreds of thousands) would show up as hot spots in ANTS. When I worked to reduce the number of calls, my app would fail to show improvement. ANTS would show me the next hot spot and I would be fooled again.   

The reason for this (as I theorized) was that ANTS was measuring the profile time in addition to the method time. The profile time outweighed the execution time, thus erroneously pointed out a hot spot. Admittedly, reducing the call count was good for my software but it wasn't my primary goal for profiling. Eventually, I would find the real bottleneck and fix it. Ultimately, the tool did a lot of good for our software.   

<span style="font-weight: bold">The New ANTS 4.0</span>   
When version 4.0 of ANTS came out, I was very interested to see what they had to offer. I was pleasantly surprised with what I found. The ANTS team completely re-wrote their profiling engine AND the UI. Both of these re-writes have been a home run thus far.   

<span style="font-weight: bold">Accuracy    
</span>The most important improvement is the accuracy of the results. I have used the new product to find several hot spots. This time around, it is extremely accurate. For instance, when I profiled a particular action, ANTS found a hot spot that was taking 27% of the time. When I implemented caching for that particular code and timed the action again, I got a 26% improvement from my fix. This is the type of accuracy I have been waiting for. I have had several other successful finds like that.   

One other notable feature: recognition of the JIT compile time. In one particular action with a rather deep call stack, ANTS was able to tell me that 38% of my time was spent in the JIT compilation. It was useful to know where the time was being spent.   

<span style="font-weight: bold">The Timeline</span>   
OK, now to the UI improvements. ANTS 4.0 features an interactive time line of the execution. You have the ability to track any system counters, but it tracks the processor utilization by default. As it goes, it marks the important events such as button clicks and window events. At any given time, you can select any subset of the time to profile. You can even bookmark those subsets so you can look at it later. They even allow you to zoom in to the graph. In all, this is a GREAT feature.   
![](http://bilzmoude.googlepages.com/Ants.Navigation.GIF)   
<span style="font-weight: bold">Tree View</span>   
In the previous versions, the primary view of the data was a data grid. The data grid has not been removed in 4.0, but it is no longer the primary view. Instead, it is a tree view. This is great as it is a much more natural visualization of the data.   
![](http://bilzmoude.googlepages.com/Ants.TreeView.GIF)   

<span style="font-weight: bold">Graph View</span>   
As nice as the tree view is for finding problems, the graph view is best for communicating bottlenecks to others. You have control over which nodes in this graph are shown. Take a look:   
![](http://bilzmoude.googlepages.com/Ants.GraphView.GIF)   

<span style="font-weight: bold">Code View</span>   
Finally, the code view is similar to previous versions of ANTS. One notable new feature is the ability to navigate the code from this view. You can now click on the methods in the view and it will take you to that method. I have found myself using it a lot.   
![](http://bilzmoude.googlepages.com/Ants.CodeView.GIF)   

<span style="font-weight: bold">Conclusion</span>   
In all, this is more than a version upgrade. It is mostly a new tool. You can find several similarities with the old tool, but you will find more differences... in a good way. Kudos to the team that put this together. It has become one of my favorite tools in my development tool belt. <span style="font-weight: bold">   
</span>