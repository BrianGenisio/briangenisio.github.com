---
title: Introducing DynamicWrapper
layout: post
category: Software
tags: [OSS, DynamicWrapper]
permalink: /archives/2009/09/18/introducing-dynamicwrapper/
---

[DynamicWrapper project on Github](https://github.com/skyguy94/dynamic-wrapper)

**_Edit: DynamicWrapper does not work in SIlverlight as I first though.&nbsp; My unit tests passed, but in runtime I get an exception when I try to use the class.&nbsp; I will be looking at some alternatives._**

Over the past few weeks, I have been working on a utility that I have wanted for years: something that allows me to apply an interface to an object that matches the contract but doesn’t actually implement the interface.&nbsp; In other words, I’ve wanted duck typing in C#.&nbsp; The code and project can be found on [CodePlex](http://www.codeplex.com/).&nbsp; It is set up so you simply copy one file into your solution and use the extension methods.

Why did I want this?&nbsp; Because I write a lot of unit tests and I develop in .Net with C#.&nbsp; Because I write way too many wrapper classes to make my code testable.&nbsp; For example, lets say that I have a class that I want to test and it acts upon a framework object:

```csharp
public sealed class FrameworkClass
{
    internal FrameworkClass() {}

    public int X {get; set;}
    public int Y {get; set;}
    public int Calculate() { return DoSomeStuff(); }
}

public class MyClass
{
    public void DoSomethingWithFrameworkData(FrameworkClass value)
    {
        // Do Something
    }
}
```

The problem is that I can’t test **MyClass** because it is dependent upon **FrameworkClass** which I can never construct myself (it is sealed with an internal constructor and no interface).&nbsp; The solution for this is simple but tedious – create a wrapper class that implements an interface and proxy through the wrapper class to the real object.&nbsp; This approach works, but I can say this: **_I am sick of writing wrapper classes!_**

This is why I created this DynamicWrapper utility.&nbsp; It exposes two extension methods: realObject.As&lt;Interface&gt;() and wrapper.AsReal&lt;ConcreteClass&gt;().&nbsp; It uses Reflection to emit a dynamically generated wrapper class that implements the interface, and wraps your object for you.&nbsp; It sounds complicated, but it is extremely simple to use.

Here is an example. Start by creating an interface that looks like the FrameworkClass:

```csharp
public interface ICalculatable
{
    int X {get; set;}
    int Y {get; set;}
    int Calculate();
}
```

Modify your class to depend on ICalculatable:

```csharp
public void DoSomethingWIthFrameworkData(ICalculatable value) {}
```

Now, when you pass the framework class into the MyClass, you can wrap it with the interface:

```csharp
myObject.DoSomethingWithFrameworkData(frameworkObject.As<ICalculatable>());
```

If you need the framework object to pass back to the framework, it is really simple:

```csharp
wrapper.AsReal<FrameworkClass>()
```

That’s all there is to it!&nbsp; The utility is very simple.&nbsp; It just gets me out of the business of writing (and maintaining) wrapper classes. I am now free to get back to real development.

_*On a slightly related note, I will be focusing my learning efforts towards Ruby in the coming months.*_