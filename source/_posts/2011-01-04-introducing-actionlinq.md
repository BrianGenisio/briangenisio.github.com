---
title: Introducing… ActionLinq
date: 2011-01-04 03:02:48
layout: post
category: Software
tags: [ActionLinq, Flash, Flex, Linq]
permalink: /archives/2011/01/04/introducing-actionlinq/
---

[![](https://bitbucket.org/briangenisio/actionlinq/raw/395b49a6b4ec/Assets/ActionLINQ.png)](https://bitbucket.org/briangenisio/actionlinq/)

I am excited to release a pet project of mine that I have been developing for a few months.&#160; [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) is a complete [LINQ-to-Objects](http://msdn.microsoft.com/library/bb397919.aspx) implementation for ActionScript 3.0.&#160; Including the same deferred execution behavior as LINQ, [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) is a functional query library designed to process and manipulate data in ActionScript 3.0.&#160; It is especially useful for processing data received from web services and manipulating it to fit into the view.

Let me illustrate what [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) can do with an example of some basic data processing:
```actionscript
var transformed:Array =
        [-4, -3, -2, -1, 0, 1, 2, 3, 4]
        .where(isEven)
        .select(square)
        .distinct()
        .reverse()
        .toArray();

assertThat(transformed, array(0, 4, 16));
```

[ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) can query pretty much any collection type (Array, ArrayCollection, ArrayList, XMLList, Vector, etc) and it can output it just the same.&#160; 

Lets look at a slightly more real-world example.&#160; Assume that the **xml** variable is data received from something like a REST service.&#160; We want to create an ordered collection of distinct categories from the XML that was received:

```actionscript
var xml = <products>
              <product name="Beef"         category="Meat" />
              <product name="Hot Dog Buns" category="Bread" />
              <product name="Bran Flakes"  category="Cereal" />
              <product name="Chicken"      category="Meat" />
              <product name="White Bread"  category="Bread" />
              <product name="Pita Bread"   category="Bread" />
          </products>;

var categories:ArrayCollection =
        Enumerable.from(xml.product)
        .select(function(product):String { return product.@category })
        .distinct()
        .orderBy(identity)
        .toArrayCollection();
```

Running that query lets you bind the **categories** variable to a **DropDownList** with a very expressive, functional query on the data that was received.

These are just a few examples.&#160; You can find a lot more documentation at the [project site](https://bitbucket.org/briangenisio/actionlinq/).

## How do I Get ActionLinq?

I have made [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) an open-source project on [BitBucket.org](https://bitbucket.org) under the Apache 2.0 license.&#160; I chose that license because I want you to be able to use it for any use without charge as long as you maintain attribution.&#160; In other words, fork it, modify it, re-distribute it, do whatever for any reason.&#160; Just give me and/or SRT Solutions credit for the work that ActionLinq provides.

The BitBucket site includes a lot of documentation for [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) in the Wiki.&#160; There are also a lot of unit tests in the source which document everything that [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) is meant to do.

If you use it, let me know!&#160; I’d love to know who is using it.&#160; If you find problems, also let me know in the “Issues” section of the project site.

## Why Did I Create ActionLinq?

### Overcome the Flex Collections Inconsistencies

When I first came to Flex, I was frustrated by the inconsistencies between the different collection types.&#160; Some types, like Array, include useful functions like **map** and **filter** (**select** and **where** in [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/)) but other types like ArrayList do not.&#160; Although an interface named **IList** exists, most of the collections do not implement it.&#160; Some collections use a **length** property.&#160; Others use a **length()** method.&#160; The **Vector** class gives a useful typed data set but is difficult to go back and forth between it and other collection types.&#160; Inconsistencies forced me to convert my data collections from one collection type to another just to get the functionality I needed. I found myself writing more code than I wanted to work my data. Still, I was missing many of the useful capabilities that I was used to from my Silverlight and .Net development.

I quickly realized that what I really wanted was LINQ.&#160; I threw together a proof of concept by implementing **select**, **where**, and **selectMany**.&#160; I found myself using these methods throughout my application.&#160; As I found myself wanting another LINQ capability, I added it to my [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) library.&#160; The next thing I knew: I had a mostly complete LINQ-to-Objects implementation.&#160; A bit more work and I had a complete clone of LINQ-to-Objects.

### Aid .Net Developers in Flex Development

I have been preaching lately that Silverlight and Flex developers need to get together and learn from one another.&#160; Where Silverlight has some great features (LINQ is one of them), Flex has some pretty great features as well.&#160; I believe that a library like [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) will facilitate the learning process from Silverlight to Flex by leveraging existing Silverlight knowledge.

### Get to Know LINQ

There is no better way to fully understand the power and intricacies of LINQ in Silverlight and .Net than by implementing it on a different platform.&#160; By implementing every function and every overload in the LINQ space, I was forced to understand what everything is used for because I had to write unit tests that gave examples of using the functionality.

### Get to Know Flex and ActionScript 3

There is no better way to get to know a new environment than to implement a familiar set of functionality in it.&#160; I was forced to learn about areas of Flex and ActionScript 3.0 that I would not have likely done on my own.&#160; I learned all about functional programming and closures in AS3, unit testing in Flex, the dynamic capabilities of AS3, monkey patching and ASDoc to name a few.&#160; I have come out of the [ActionLinq](https://bitbucket.org/briangenisio/actionlinq/) project with a strong understanding of the ActionScript 3.0 language and the more esoteric but powerful features of the language.&#160; I was also painfully made aware of the limitations of ActionScript that I miss from C#: Generics, method overloading, operator overloading, yielding and lambdas to name a few.

## Go Use It!

Give it a try.&#160; Let me know how it goes.&#160; Have fun.&#160; Enjoy!
