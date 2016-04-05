---
title: 'Cross-Training in Silverlight & Flex -- C#/CLR vs ActionScript3/AVM Part 1'
tags:
  - ActionScript
  - 'C#'
  - Cross-Training
  - Flex
  - RIA
  - Silverlight
id: 197
categories:
  - Uncategorized
date: 2010-12-17 02:59:22
---

More [Cross-Training in Silverlight &amp; Flex](http://houseofbilz.com/archives/2010/10/24/cross-training-in-silverlight-vs-flex/)
 [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchives%2F2010%2F12%2F17%2Fcross-training-in-silverlight-flex-cclr-vs-actionscript3avm-part-1%2F)](http://dotnetshoutout.com/Cross-Training-in-Silverlight-Flex-%E2%80%94-CCLR-vs-ActionScript3AVM-Part-1)   

In my previous post, I talked about the [declarative side of Silverlight and Flex](http://houseofbilz.com/archives/2010/11/08/cross-training-in-silverlight-and-flex-xaml-vs-mxml/) (XAML vs MXML).&#160; In this post, I will talk about the [imperative](http://en.wikipedia.org/wiki/Imperative_programming) side Silverlight and Flex.&#160; In other words, this is where developers write code that executes logic to modify state.&#160; The primary languages for Silverlight and Flex are C# and ActionScript (there are others) and the execution run-times are the Common Language Runtime (CLR) and the ActionScript Virtual Machine (AVM).
  <div style="background-color: #e8f3ff">   

### C#/CLR

The most widely used language in Silverlight is C#, a C-like language that is easy to use/read and it is very powerful.&#160; It is an object-oriented language, though it borrows concepts from functional languages.&#160; C# code compiles down to byte code which gets executed on the Common Language Runtime (CLR).&#160; As its name implies, the CLR can run languages other than C#.&#160; The supported language list is quite long, but some of the more common options are [VisualBasic.Net](http://msdn.microsoft.com/en-us/vbasic/default), [F#](http://msdn.microsoft.com/en-us/fsharp/default), [IronRuby](http://ironruby.net/) and [IronPython](http://ironpython.codeplex.com/).&#160; Since C# is the most commonly used, I will focus on it in this post.

#### <font style="font-weight: bold">Hello World</font>

To start things off, here is a “Hello World” application written in C#.&#160; It is quite similar to ActionScript because they are both object-oriented languages.
    <pre class="brush: csharp; ruler: true; gutter: false; toolbar: false;">class Program
{
    static void Main(string[] args)
    {
        Console.WriteLine(&quot;Hello World&quot;);            
    }
}</pre>

#### &#160;

#### <font style="font-weight: bold">Static by Default, Dynamic by Option</font>

Since its inception, C# has been a static language.&#160; Microsoft introduced optional dynamic typing in C# 4.0 with the **dynamic** keyword.&#160; In other words, variables defined as dynamic use late binding to resolve properties and methods where all other variables are checked at compile-time.

  <pre class="brush: csharp; ruler: true; gutter: false; toolbar: false;">// STATIC EXAMPLE
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
Console.WriteLine(item.NotThere); // Runtime Error</pre>

#### &#160;

#### <font style="font-weight: bold">Classes/Interfaces</font>

C# defines interfaces, classes and derived classes very similar to ActionScript or Java:

  <pre class="brush: csharp; ruler: true; gutter: false; toolbar: false;">public interface IGobbleable
{
    void Gobble();
}

public class Turkey : IGobbleable
{
    public int Weight { get { return 5; } }

    public virtual void Gobble()
    {
        Console.WriteLine(&quot;Gobble Gobble&quot;);
    }
}

public class WildTurkey : Turkey
{
    public override void Gobble()
    {
        Console.WriteLine(&quot;Putts Putts&quot;);
    }
}</pre>

In “Part 2” of my imperative programming comparison, I will cover Generics, Iterators, Attributes, Lambdas and XML Literals.

</div>

#### &#160; 

<div style="background-color: #ffe8e8">

### ActionScript3/AVM

The most widely used language in Flex is ActionScript3, an [ECMAScript](http://www.ecmascript.org/) dialect that is easy to use/read and is quite powerful (though not as powerful as C#, in my opinion).&#160; Although it is often compared to another widely used ECMAScript language, [JavaScript](http://en.wikipedia.org/wiki/JavaScript), it diverges in a way that feels more like Java.&#160; Like Java, ActionScript3 is object-oriented with classes and interfaces.&#160; It also has optional static typing which JavaScript does not have.&#160; ActionScript3 code compiles down to ActionScript Byte Code (ABC) and runs on the ActionScript Virtual Machine (AVM2) – also known as [Tamarin](http://www.mozilla.org/projects/tamarin/).&#160; 

Although ActionScript is the only official language supported on the AVM2, there are several options for developing in Flex.&#160; Adobe Labs released a preview of [Alchemy](http://labs.adobe.com/technologies/alchemy/), a tool for compiling C/C++ code to run on the AVM2.&#160; The widely-known [haXe](http://haxe.org/) language, which is like ActionScript on steroids, also compiles for the AVM2.&#160; In addition, people have gotten Ruby (via [HotRuby](http://hotruby.yukoba.jp/)), C# (via [cs2as](http://cs2as.codeplex.com/)J) and Java (via [J2AS3](http://code.google.com/p/j2as3/)) to run in the Flash player – therefore lending to Flex development.

Since ActionScript3 is the most commonly used language in Flex, I will focus on it in this post.

#### <font style="font-weight: bold">Hello World</font>

To start things off, here is a “Hello World” application written in ActionScript3.&#160; It is quite similar to C# because they are both object-oriented languages.

  <pre class="brush: as3; ruler: true; gutter: false; toolbar: false;">public class HelloWorld extends Sprite
{
    public function HelloWorld()
    {
        trace(&quot;Hello World&quot;);
    }
}</pre>

#### &#160;

#### <font style="font-weight: bold">Dynamic by Default, Static by Option</font>

Since its inception, ActionScript has been a dynamic language.&#160; Adobe introduced optional static typing in ActionScript 2 with a colon syntax.&#160; In other words, variables defined as static will remain static and will be checked at compile-time where variables without type information will be dynamic and late-bound (run-time).

  <pre class="brush: as3; ruler: true; gutter: false; toolbar: false;">// DYNAMIC EXAMPLE
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
trace(steamShovel.NotThere); // Compile-time Error</pre>

#### &#160;

#### <font style="font-weight: bold">Classes/Interfaces</font>

ActionScript defines interfaces, classes and derived classes similar to C# with a syntax that is more like Java:

  <pre class="brush: as3; ruler: true; gutter: false; toolbar: false;">public interface IGobbleable
{
    function gobble():void;
}

public class Turkey implements IGobbleable
{
    public var weight:int = 5;

    public function gobble():void
    {
        trace(&quot;Gobble Gobble&quot;);
    }
}

public class WildTurkey extends Turkey
{
    public override function gobble():void
    {
        trace(&quot;Putts Putts&quot;);
    }
}</pre>

In “Part 2” of my imperative programming comparison, I will cover Generics (via Vector.&lt;&gt;), Proxy objects, MetaData Tags, Anonymous Functions and XML Literals.

</div>

Stay tuned ![Smile](http://houseofbilz.com/wp-content/uploads/2010/12/wlEmoticon-smile.png)