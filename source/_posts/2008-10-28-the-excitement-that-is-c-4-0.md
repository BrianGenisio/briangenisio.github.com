---
title: 'The Excitement that is C# 4.0'
id: 43
categories:
  - Uncategorized
date: 2008-10-28 00:53:00
tags:
---

[![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fbrian.genisio.org%2f2008%2f10%2fexcitement-that-is-c-40.html)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fbrian.genisio.org%2f2008%2f10%2fexcitement-that-is-c-40.html)   
I just got back from the &quot;Future of C#&quot; talk at PDC by Anders Hejlsberg. This was a truly inspiring talk for a geek like me. C# is evolving into a much more dynamic language. I have always been a believer of strong typing... except when I'm not... and I have been wishing for something more dynamic (such as Duck Typing). In C# 4.0, we will be seeing some significant dynamic features.   

In reality, the thing that has kept me away from using languages such as IronPython and IronRuby is their interoperability with strongly typed languages. I really believe in the concept of &quot;The right language for the job&quot;, but I hate the idea of sticking to that one language for the entire project. With the dynamic capabilities in C#, it will be MUCH easier to talk to Python or Ruby code. If I need to implement something really loosely (like a calculation engine), I will be able to jump into something loose. Then, when I want to work with that code in my more strongly typed environment, I will have that ability. The &quot;Right Language for the Job&quot; paradigm has just become much finer grained.   

So here are the details.   

<span style="font-weight: bold">Dynamic Keyword</span>   
First and foremost is the <span style="font-weight: bold">dynamic</span> keyword. This is kind of like using the <span style="font-weight: bold">object</span> keyword, but you are saying that all of your binding will be at runtime. You will loose your intelisense, of course, but you will now be able to call into methods that have not been previously defined.   

The neat thing about this is that you can make your statically defined classes be dynamic by implementing the IDynamicObject interface, which allows you to have access to the late binding calls.   

<span style="font-weight: bold">Named and Optional Parameters</span>   
Next is something that C++ has and C# has needed for a long time â€“ Optional Parameters. You can set defaults in your method declaration and the caller doesn't need to specify the parameters. In addition, you can name the parameters in the method calls. This is really great for readability... especially when you are passing a bool into a method that you have no idea what it does.   

<span style="font-weight: bold">Better COM Interoperability</span>   
These previous features (Optional and Named Parameters) are really useful to add to the new COM Interoperability features. Basically, pairing the dynamic and parameter features, talking to COM controls looks very natural.   

<span style="font-weight: bold">Covariants and Contravariants</span>   
Finally, but certainly not least, we are getting covariance and contravariance. This is something that has bugged me since I have started with C#. Currently, if a method takes IEnumerable&lt;BaseType&gt;, you can't pass IEnumerable&lt;DerivedType&gt;. I Hate having to convert the derived set to a base set just to pass it in. In C# 4.0, this will be fixed.   

<span style="font-weight: bold">Looking Forward</span>   
In future version of C#, we will start to see hooks into the compiler. This will open the door for Domain Specific Languages (DSL) and Meta Programming. We will also see the C# compiler as an embedded service within .NET. This means that we can run Eval(&quot;doSomething()&quot;). It also means that we can start to think about embedding C# as a scripting language for our application... much like Javascript in the HTML DOM.   

I am hoping to see C# 4.0 soon. Better yet, I am hoping to see C# 4.0 in the bits we get tomorrow!   