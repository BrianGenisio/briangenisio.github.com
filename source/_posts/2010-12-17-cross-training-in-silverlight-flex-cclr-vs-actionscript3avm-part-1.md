---
title: 'Cross-Training in Silverlight & Flex -- C#/CLR vs ActionScript3/AVM Part 1'
date: 2010-12-17 02:59:22
layout: post
category: Software
tags: [ActionScript, C#, Cross-Training, Flex, RIA, Silverlight]
permalink: /archives/2010/12/17/cross-training-in-silverlight-flex-cclr-vs-actionscript3avm-part-1/
---

More [Cross-Training in Silverlight &amp; Flex](/archives/2010/10/24/cross-training-in-silverlight-vs-flex/) 

In my previous post, I talked about the [declarative side of Silverlight and Flex](/archives/2010/11/08/cross-training-in-silverlight-and-flex-xaml-vs-mxml/) (XAML vs MXML).&#160; In this post, I will talk about the [imperative](http://en.wikipedia.org/wiki/Imperative_programming) side Silverlight and Flex.&#160; In other words, this is where developers write code that executes logic to modify state.&#160; The primary languages for Silverlight and Flex are C# and ActionScript (there are others) and the execution run-times are the Common Language Runtime (CLR) and the ActionScript Virtual Machine (AVM).

### C#/CLR

The most widely used language in Silverlight is C#, a C-like language that is easy to use/read and it is very powerful.&#160; It is an object-oriented language, though it borrows concepts from functional languages.&#160; C# code compiles down to byte code which gets executed on the Common Language Runtime (CLR).&#160; As its name implies, the CLR can run languages other than C#.&#160; The supported language list is quite long, but some of the more common options are [VisualBasic.Net](http://msdn.microsoft.com/en-us/vbasic/default), [F#](http://msdn.microsoft.com/en-us/fsharp/default), [IronRuby](http://ironruby.net/) and [IronPython](http://ironpython.codeplex.com/).&#160; Since C# is the most commonly used, I will focus on it in this post.

#### Hello World

To start things off, here is a “Hello World” application written in C#.&#160; It is quite similar to ActionScript because they are both object-oriented languages.
```csharp
class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine("Hello World");
    }
}
```
#### Static by Default, Dynamic by Option

Since its inception, C# has been a static language.&#160; Microsoft introduced optional dynamic typing in C# 4.0 with the **dynamic** keyword.&#160; In other words, variables defined as dynamic use late binding to resolve properties and methods where all other variables are checked at compile-time.

```csharp
// STATIC EXAMPLE
Turkey turkey = new Turkey();
Console.WriteLine(turkey.Weight);
var steamShovel = new SteamShovel(); // Implicit Typing
Console.WriteLine(steamShovel.Weight);
Console.WriteLine(steamShovel.NotThere); // Compile-time Error

// DYNAMIC EXAMPLE
dynamic item = new Turkey();
Console.WriteLine(item.Weight);
item = new SteamShovel();
Console.WriteLine(item.Weight);
Console.WriteLine(item.NotThere); // Runtime Error
```

#### Classes/Interfaces

C# defines interfaces, classes and derived classes very similar to ActionScript or Java:

```csharp
public interface IGobbleable
{
    void Gobble();
}

public class Turkey : IGobbleable
{
    public int Weight { get { return 5; } }

    public virtual void Gobble()
    {
        Console.WriteLine("Gobble Gobble");
    }
}

public class WildTurkey : Turkey
{
    public override void Gobble()
    {
        Console.WriteLine("Putts Putts");
    }
}
```

In “Part 2” of my imperative programming comparison, I will cover Generics, Iterators, Attributes, Lambdas and XML Literals.



### ActionScript3/AVM

The most widely used language in Flex is ActionScript3, an [ECMAScript](http://www.ecmascript.org/) dialect that is easy to use/read and is quite powerful (though not as powerful as C#, in my opinion).&#160; Although it is often compared to another widely used ECMAScript language, [JavaScript](http://en.wikipedia.org/wiki/JavaScript), it diverges in a way that feels more like Java.&#160; Like Java, ActionScript3 is object-oriented with classes and interfaces.&#160; It also has optional static typing which JavaScript does not have.&#160; ActionScript3 code compiles down to ActionScript Byte Code (ABC) and runs on the ActionScript Virtual Machine (AVM2) – also known as [Tamarin](http://www.mozilla.org/projects/tamarin/).&#160; 

Although ActionScript is the only official language supported on the AVM2, there are several options for developing in Flex.&#160; Adobe Labs released a preview of [Alchemy](http://labs.adobe.com/technologies/alchemy/), a tool for compiling C/C++ code to run on the AVM2.&#160; The widely-known [haXe](http://haxe.org/) language, which is like ActionScript on steroids, also compiles for the AVM2.&#160; In addition, people have gotten Ruby (via [HotRuby](http://hotruby.yukoba.jp/)), C# (via [cs2as](http://cs2as.codeplex.com/)J) and Java (via [J2AS3](http://code.google.com/p/j2as3/)) to run in the Flash player – therefore lending to Flex development.

Since ActionScript3 is the most commonly used language in Flex, I will focus on it in this post.

#### Hello World

To start things off, here is a “Hello World” application written in ActionScript3.&#160; It is quite similar to C# because they are both object-oriented languages.

```actionscript
public class HelloWorld extends Sprite
{
    public function HelloWorld()
    {
        trace("Hello World");
    }
}
```

#### Dynamic by Default, Static by Option

Since its inception, ActionScript has been a dynamic language.&#160; Adobe introduced optional static typing in ActionScript 2 with a colon syntax.&#160; In other words, variables defined as static will remain static and will be checked at compile-time where variables without type information will be dynamic and late-bound (run-time).

```actionscript
// DYNAMIC EXAMPLE
var item = new Turkey();
trace(item.weight);
item = new SteamShovel();
trace(item.weight);
trace(item.NotThere); // Runtime Error

// STATIC EXAMPLE
var turkey:Turkey = new Turkey();
trace(turkey.weight);
var steamShovel:SteamShovel = new SteamShovel();
trace(steamShovel.weight);
trace(steamShovel.NotThere); // Compile-time Error
```
#### <font style="font-weight: bold">Classes/Interfaces</font>

ActionScript defines interfaces, classes and derived classes similar to C# with a syntax that is more like Java:

```actionscript
public interface IGobbleable
{
    function gobble():void;
}

public class Turkey implements IGobbleable
{
    public var weight:int = 5;

    public function gobble():void
    {
        trace("Gobble Gobble");
    }
}

public class WildTurkey extends Turkey
{
    public override function gobble():void
    {
        trace("Putts Putts");
    }
}
```

In “Part 2” of my imperative programming comparison, I will cover Generics (via Vector.&lt;&gt;), Proxy objects, MetaData Tags, Anonymous Functions and XML Literals.


Stay tuned 