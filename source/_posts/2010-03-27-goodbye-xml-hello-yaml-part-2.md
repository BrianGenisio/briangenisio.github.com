---
title: Goodbye XML… Hello YAML (part 2)
date: 2010-03-27 14:00:00
layout: post
category: Software
tags: [XML, YAML]
permalink: /archives/2010/03/27/goodbye-xml-hello-yaml-part-2/
---

[Part 1](/archives/2010/02/21/goodbye-xml-hello-yaml-part-1/)

After I explained my motivation for using YAML instead of XML for my data, I got a lot of people asking me what type of tooling is available in the .Net space for consuming YAML.&#160; In this post, I will discuss a nice tooling option as well as describe some small modifications to leverage the extremely powerful dynamic capabilities of C# 4.0.&#160; I will be referring to the following YAML file throughout this post

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

### Tooling

It turns out that there are several implementations of YAML tools out there.&#160; The neatest one, in my opinion, is [YAML for .NET, Visual Studio and Powershell](http://yaml.codeplex.com/).&#160; It includes a great editor plug-in for Visual Studio as well as YamlCore, which is a parsing engine for .Net.&#160; It is in active development still, but it is certainly enough to get you going with YAML in .Net.&#160; 

Start by referenceing YamlCore.dll, load your document, and you are on your way.&#160; Here is an example of using the parser to get the title of the Recipe:

```csharp
var yaml = YamlLanguage.FileTo("Data.yaml") as Hashtable;
var recipe = yaml["Recipe"] as Hashtable;
var title = recipe["Title"] as string;
```

In a similar way, you can access data in the Ingredients set:

```csharp
var yaml = YamlLanguage.FileTo("Data.yaml") as Hashtable;
var recipe = yaml["Recipe"] as Hashtable;
var ingredients = recipe["Ingredients"] as ArrayList;

foreach (Hashtable ingredient in ingredients)
{
    var name = ingredient["Name"] as string;
}
```

You may have noticed that YamlCore uses non-generic Hashtables and ArrayLists.&#160; This is because YamlCore was designed to work in all .Net versions, including 1.0.&#160; Everything in the parsed tree is one of two things: Hashtable, ArrayList or Value type (usually String).&#160; This translates well to the YAML structure where everything is either a Map, a Set or a Value.&#160; 

### Taking it further

Personally, I really dislike writing code like this.&#160; Years ago, I promised myself to never write the words Hashtable or ArrayList in my .Net code again.&#160; They are ugly, mostly depreciated collections that existed before we got generics in C# 2.0.&#160; Now, especially that we have dynamic capabilities in C# 4.0, we can do **a lot** better than this.&#160; With a relatively small amount of code, you can wrap the Hashtables and Array lists with a dynamic wrapper (wrapper code at the bottom of this post).&#160; The same code can be re-written to look like this:

```csharp
dynamic doc = YamlDoc.Load("Data.yaml");
var title = doc.Recipe.Title;
```

And

```csharp
dynamic doc = YamlDoc.Load("Data.yaml");
foreach (dynamic ingredient in doc.Recipe.Ingredients)
{
    var name = ingredient.Name;
}
```

I significantly prefer this code over the previous.&#160; That’s not all… the magic really happens when we take this concept into WPF.&#160; With a single line of code, you can bind to the data dynamically in the view:

```csharp
DataContext = YamlDoc.Load("Data.yaml");
```

Then, your XAML is extremely straight-forward (Nothing else.&#160; No static types, no adapter code.&#160; Nothing):

```xml
<StackPanel>
    <TextBlock Text="{Binding Recipe.Title}" />
    <TextBlock Text="{Binding Recipe.Description}" />
    <TextBlock Text="{Binding Recipe.Author}" />
    <TextBlock Text="{Binding Recipe.TimeToPrepare}" />

    <TextBlock Text="Ingredients:" FontWeight="Bold" />
    <ItemsControl ItemsSource="{Binding Recipe.Ingredients}" Margin="10,0,0,0">
        <ItemsControl.ItemTemplate>
            <DataTemplate>
                <StackPanel Orientation="Horizontal">
                    <TextBlock Text="{Binding Quantity}" />
                    <TextBlock Text=" " />
                    <TextBlock Text="{Binding Units}" />
                    <TextBlock Text=" of " />
                    <TextBlock Text="{Binding Name}" />
                </StackPanel>
            </DataTemplate>
        </ItemsControl.ItemTemplate>
    </ItemsControl>

    <TextBlock Text="Steps:" FontWeight="Bold" />
    <ItemsControl ItemsSource="{Binding Recipe.Steps}" Margin="10,0,0,0">
        <ItemsControl.ItemTemplate>
            <DataTemplate>
                <StackPanel Orientation="Horizontal">
                    <TextBlock Text="{Binding Number}" />
                    <TextBlock Text=": " />
                    <TextBlock Text="{Binding Description}" />
                </StackPanel>
            </DataTemplate>
        </ItemsControl.ItemTemplate>
    </ItemsControl>

</StackPanel>
```

This nifty XAML binding trick only works in WPF, unfortunately.&#160; Silverlight handles binding differently, so they don’t support binding to dynamic objects as of late (March 2010).&#160; This, in my opinion, is a major lacking feature in Silverlight and I **really** hope we will see this feature available to us in Silverlight 4 Release.&#160; (I am not very optimistic for Silverlight 4, but I can hope for the feature in Silverlight 5, can’t I?)

### Conclusion

I still have a few things I want to say about using YAML in the .Net space including de-serialization and using IronRuby for your YAML parser, but this post is hopefully enough to see how easy it is to incorporate YAML documents in your code.

[Codeplex Site for YAML tools](http://yaml.codeplex.com/ "http://yaml.codeplex.com/")

[Dynamic wrapper for YamlCore](http://pastie.org/890406)