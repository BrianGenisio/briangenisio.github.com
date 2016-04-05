---
title: Focusing on Functional
id: 45
categories:
  - Uncategorized
date: 2008-10-30 17:29:00
tags:
---

I have been toying with functional programming a bit lately. I have been using lambdas and Linq when it has made sense in my code. I downloaded the F# compiler tools and mucked around with it a tiny bit. I read a few blogs that talk about functional programming concepts, etc. I have enjoyed reading the elegance of the paradigm, but never really got into it much. 

But then I sat in on an &quot;Open Space&quot; session where Scott Guthrie was talking. Most of it strayed from the standard &quot;Open Space&quot; format and was more of a Q&amp;A, but I guess this happens sometimes when big names show up. Anyways, one thing he said really stuck with me. It may not be a new idea, but it really resonated with me... I just hadn't thought about it this way before. 

He said that in functional programming, you are declaring WHAT you want to do, instead of HOW you want to do it. In other words, in a functional language, you might describe a pattern to select from a collection (WHAT). In a more procedural language, you would do the same thing with a &quot;foreach&quot; loop (HOW). 

This distinction isn't just semantic. It is extremely important for parallel programming. A loop is very hard to run in parallel since the compiler has a difficult time determining side effects. When you describe what you want done in a functional language, your compiler/framework CAN add parallelism. You let your tools figure out how to do the work in a parallel way. 

It became clear to me that I need to take functional programming more seriously. Our hardware is no longer increasing in speed. Instead, the number of processors increase our computing power. If we can't figure out how to spread the work over multiple workers, we will never be able to take advantage of that power. 

Functional languages have the potential to execute in parallel much more than procedural languages. I am making a promise to myself that I will become much more proficient in the functional paradigm. It seems to be the responsible thing to do.