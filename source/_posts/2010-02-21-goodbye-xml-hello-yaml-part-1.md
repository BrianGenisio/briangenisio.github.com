---
title: Goodbye XML… Hello YAML (part 1)
date: 2010-02-21 13:58:00
layout: post
category: Software
tags: [XML, YAML]
permalink: /archives/2010/02/21/goodbye-xml-hello-yaml-part-1/
---

[Part 2](/archives/2010/03/27/goodbye-xml-hello-yaml-part-2/)

This is the first in a many-part series in which I will be writing about using YAML in the .Net space – particularly within C#.&#160; I will cover the whys, the hows, and show some tricks using the dynamic capabilities of C# when using YAML.&#160; I might even explore IronRuby a bit.

##### Why YAML?

I got the chance to sit in on four days of Ruby on Rails (RoR) training from [Joe Obrien](http://objo.com/) a few weeks ago.&#160; I hadn’t played with RoR in a few months, and this training was amazingly useful.&#160; I always like to see what I can take away from an environment like this for the work I do more regularly.

In this case, one of the things I took away with me was YAML.&#160; Let me start with some background.

Over 10 years ago, when XML was touted as a human-readable data format, I had to scoff.&#160; Was XML really human readable?&#160; Well, from a software developer’s perspective, it sure is a lot nicer than binary.&#160; But, from a REAL human’s perspective, XML is just barely more readable than Klingon.&#160; This is mostly due to the verbose nature of XML and the often ranted about “[angle bracket tax](http://www.google.com/search?q=angle+bracket+tax)”.&#160;&#160;&#160; 

10 years later, we are still using XML as our primary data transfer/persistence/definition format.&#160; I certainly don’t mind using XML in a world where I (a human) rarely need to read the data.&#160; I am fine with keeping the data in my REST services as XML, for instance.&#160; In that case, my tools abstract it away and I don’t have to read it very often.

Currently, my colleague [Mike Woelmer](http://srtsolutions.com/blogs/mikewoelmer/) and I have a client who needs us to develop an engine where the business rules will be entered by a human (not a developer) and will change as the project evolves.&#160; The data/rules will be set in stone once the project is complete.&#160; XML is a bad choice for this, since a non-technical person will be entering the rules.&#160; At the same time, developing a UI for this is too time consuming and out of the scope of the project.&#160; My first thought: a [Doman Specific Language](http://en.wikipedia.org/wiki/Domain_specific_language) (DSL)!&#160; That would certainly lend well to my requirements.&#160; As much as I liked that idea, I didn’t have time to create a DSL either, especially while spiking.&#160; Then I remembered back to my RoR training.&#160; I remembered a data format called [YAML](http://yaml.org/) that they used for some of the configuration files.&#160; It was a VERY easy-to-read, hierarchical data format.

To illustrate the difference between XML and YAML, here is a simple example of some rather easy-to-read XML defining the data for a recipe (unrelated to my current project):

```xml
<recipe>
  <title>Macaroni and Cheese</title>
  <description>My favorite comfort food.</description>
  <author>Brian Genisio</author>
  <timeToPrepare>30 Minutes</timeToPrepare>
  <ingredients>
    <ingredient>
      <name>Cheese</name>
      <quantity>3</quantity>
      <units>cups</units>
    </ingredient>
    <ingredient>
      <name>Macaroni</name>
      <quantity>16</quantity>
      <units>oz</units>
    </ingredient>
  </ingredients>
  <steps>
    <step>
      <number>1</number>
      <description>Cook the macaroni</description>
    <step>
    <step>
      <number>2</number>
      <description>Melt the cheese</description>
    </step>
    <step>
      <number>3</number>
      <description>Mix the cooked macaroni with the melted cheese</description>
    </step>
  </steps>
</recipe>
```
Here is the exact same data described with YAML:

```yaml
Recipe:
  Title:         Macaroni and Cheese
  Description:   My favorite comfort food.
  Author:        Brian Genisio
  TimeToPrepare: 30 Minutes
  Ingredients:
    -
      Name:     Cheese
      Quantity: 3
      Units:    cups
    -
      Name:     Macaroni
      Quantity: 16
      Units:    oz
  Steps:
    -
      Number: 1
      Description: Cook the macaroni
    -
      Number: 2
      Description: Melt the cheese
    -
      Number: 3
      Description: Mix the cooked macaroni with the melted cheese
```

Although this is a very simple example, I would ask you: Which one would you rather present to a customer when talking about the data and business rules?&#160; Which one would YOU rather look at when developing your software?

In my next couple of posts, I will discuss some tools available to you in the .Net space and some nifty C# 4.0 techniques for working with the data.