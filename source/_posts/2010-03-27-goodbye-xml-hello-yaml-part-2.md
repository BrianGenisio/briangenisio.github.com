---
title: Goodbye XML… Hello YAML (part 2)
tags:
  - XML
  - YAML
id: 92
categories:
  - Uncategorized
date: 2010-03-27 14:00:00
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2010%2F03%2F27%2Fgoodbye-xmlhellip-hello-yaml-part-2.aspx)](http://dotnetshoutout.com/Goodbye-XML%E2%80%A6-Hello-YAML-part-2) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2010%2f03%2f27%2fgoodbye-xmlhellip-hello-yaml-part-2.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2010%2f03%2f27%2fgoodbye-xmlhellip-hello-yaml-part-2.aspx)   

[Part 1](http://houseofbilz.com/archive/2010/02/21/goodbye-xmlhellip-hello-yaml-part-1.aspx)

After I explained my motivation for using YAML instead of XML for my data, I got a lot of people asking me what type of tooling is available in the .Net space for consuming YAML.&#160; In this post, I will discuss a nice tooling option as well as describe some small modifications to leverage the extremely powerful dynamic capabilities of C# 4.0.&#160; I will be referring to the following YAML file throughout this post
    <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:9bfdd9c4-2ac1-4f6e-961e-d3dad7186bc4" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">Recipe:
  Title:         Macaroni and Cheese
  Description:   My favorite comfort food.
  Author:        Brian Genisio
  TimeToPrepare: </span><span style="color: #000000;">30</span><span style="color: #000000;"> Minutes
  Ingredients:
    -
      Name:     Cheese
      Quantity: </span><span style="color: #000000;">3</span><span style="color: #000000;">
      Units:    cups
    -
      Name:     Macaroni
      Quantity: </span><span style="color: #000000;">16</span><span style="color: #000000;">
      Units:    oz
  Steps:
    -
      Number: </span><span style="color: #000000;">1</span><span style="color: #000000;">
      Description: Cook the macaroni
    -
      Number: </span><span style="color: #000000;">2</span><span style="color: #000000;">
      Description: Melt the cheese
    -
      Number: </span><span style="color: #000000;">3</span><span style="color: #000000;">
      Description: Mix the cooked macaroni with the melted cheese</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### Tooling

It turns out that there are several implementations of YAML tools out there.&#160; The neatest one, in my opinion, is [YAML for .NET, Visual Studio and Powershell](http://yaml.codeplex.com/).&#160; It includes a great editor plug-in for Visual Studio as well as YamlCore, which is a parsing engine for .Net.&#160; It is in active development still, but it is certainly enough to get you going with YAML in .Net.&#160; 

Start by referenceing YamlCore.dll, load your document, and you are on your way.&#160; Here is an example of using the parser to get the title of the Recipe:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:67da368d-a0ea-4e95-80f0-519787bb7578" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">var yaml </span><span style="color: #000000;">=</span><span style="color: #000000;"> YamlLanguage.FileTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">Data.yaml</span><span style="color: #800000;">"</span><span style="color: #000000;">) </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> Hashtable;
var recipe </span><span style="color: #000000;">=</span><span style="color: #000000;"> yaml[</span><span style="color: #800000;">"</span><span style="color: #800000;">Recipe</span><span style="color: #800000;">"</span><span style="color: #000000;">] </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> Hashtable;
var title </span><span style="color: #000000;">=</span><span style="color: #000000;"> recipe[</span><span style="color: #800000;">"</span><span style="color: #800000;">Title</span><span style="color: #800000;">"</span><span style="color: #000000;">] </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> </span><span style="color: #0000FF;">string</span><span style="color: #000000;">;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>
[](http://yaml.codeplex.com/)

In a similar way, you can access data in the Ingredients set:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:22908ba1-a4cd-4b47-a9ee-3987407bd53c" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">var yaml </span><span style="color: #000000;">=</span><span style="color: #000000;"> YamlLanguage.FileTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">Data.yaml</span><span style="color: #800000;">"</span><span style="color: #000000;">) </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> Hashtable;
var recipe </span><span style="color: #000000;">=</span><span style="color: #000000;"> yaml[</span><span style="color: #800000;">"</span><span style="color: #800000;">Recipe</span><span style="color: #800000;">"</span><span style="color: #000000;">] </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> Hashtable;
var ingredients </span><span style="color: #000000;">=</span><span style="color: #000000;"> recipe[</span><span style="color: #800000;">"</span><span style="color: #800000;">Ingredients</span><span style="color: #800000;">"</span><span style="color: #000000;">] </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> ArrayList;

</span><span style="color: #0000FF;">foreach</span><span style="color: #000000;"> (Hashtable ingredient </span><span style="color: #0000FF;">in</span><span style="color: #000000;"> ingredients)
{
    var name </span><span style="color: #000000;">=</span><span style="color: #000000;"> ingredient[</span><span style="color: #800000;">"</span><span style="color: #800000;">Name</span><span style="color: #800000;">"</span><span style="color: #000000;">] </span><span style="color: #0000FF;">as</span><span style="color: #000000;"> </span><span style="color: #0000FF;">string</span><span style="color: #000000;">;
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

You may have noticed that YamlCore uses non-generic Hashtables and ArrayLists.&#160; This is because YamlCore was designed to work in all .Net versions, including 1.0.&#160; Everything in the parsed tree is one of two things: Hashtable, ArrayList or Value type (usually String).&#160; This translates well to the YAML structure where everything is either a Map, a Set or a Value.&#160; 

### Taking it further

Personally, I really dislike writing code like this.&#160; Years ago, I promised myself to never write the words Hashtable or ArrayList in my .Net code again.&#160; They are ugly, mostly depreciated collections that existed before we got generics in C# 2.0.&#160; Now, especially that we have dynamic capabilities in C# 4.0, we can do **a lot** better than this.&#160; With a relatively small amount of code, you can wrap the Hashtables and Array lists with a dynamic wrapper (wrapper code at the bottom of this post).&#160; The same code can be re-written to look like this:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:2df57b77-9f24-4e35-a85e-cdfdd4bd989f" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">dynamic doc </span><span style="color: #000000;">=</span><span style="color: #000000;"> YamlDoc.Load(</span><span style="color: #800000;">"</span><span style="color: #800000;">Data.yaml</span><span style="color: #800000;">"</span><span style="color: #000000;">);
var title </span><span style="color: #000000;">=</span><span style="color: #000000;"> doc.Recipe.Title;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

And

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:f4b4bf83-c762-47c5-8707-ae4afbad192e" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">dynamic doc </span><span style="color: #000000;">=</span><span style="color: #000000;"> YamlDoc.Load(</span><span style="color: #800000;">"</span><span style="color: #800000;">Data.yaml</span><span style="color: #800000;">"</span><span style="color: #000000;">);
</span><span style="color: #0000FF;">foreach</span><span style="color: #000000;"> (dynamic ingredient </span><span style="color: #0000FF;">in</span><span style="color: #000000;"> doc.Recipe.Ingredients)
{
    var name </span><span style="color: #000000;">=</span><span style="color: #000000;"> ingredient.Name;
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

I significantly prefer this code over the previous.&#160; That’s not all… the magic really happens when we take this concept into WPF.&#160; With a single line of code, you can bind to the data dynamically in the view:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:56d7052c-215c-41f6-87e5-a9cd8fbc6648" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">DataContext </span><span style="color: #000000;">=</span><span style="color: #000000;"> YamlDoc.Load(</span><span style="color: #800000;">"</span><span style="color: #800000;">Data.yaml</span><span style="color: #800000;">"</span><span style="color: #000000;">);</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Then, your XAML is extremely straight-forward (Nothing else.&#160; No static types, no adapter code.&#160; Nothing):

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:4df1ba98-3945-45a1-817b-6cedd642224f" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">StackPanel</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Recipe.Title}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Recipe.Description}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Recipe.Author}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Recipe.TimeToPrepare}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">

    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="Ingredients:"</span><span style="color: #FF0000;"> FontWeight</span><span style="color: #0000FF;">="Bold"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;"> 
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">ItemsControl </span><span style="color: #FF0000;">ItemsSource</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Recipe.Ingredients}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> Margin</span><span style="color: #0000FF;">="10,0,0,0"</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
        </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">ItemsControl.ItemTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
            </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">DataTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
                </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">StackPanel </span><span style="color: #FF0000;">Orientation</span><span style="color: #0000FF;">="Horizontal"</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Quantity}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">=" "</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Units}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">=" of "</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Name}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
                </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">StackPanel</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
            </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">DataTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
        </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">ItemsControl.ItemTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">ItemsControl</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">

    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="Steps:"</span><span style="color: #FF0000;"> FontWeight</span><span style="color: #0000FF;">="Bold"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">ItemsControl </span><span style="color: #FF0000;">ItemsSource</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Recipe.Steps}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> Margin</span><span style="color: #0000FF;">="10,0,0,0"</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
        </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">ItemsControl.ItemTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
            </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">DataTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
                </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">StackPanel </span><span style="color: #FF0000;">Orientation</span><span style="color: #0000FF;">="Horizontal"</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Number}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">=": "</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
                    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding Description}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
                </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">StackPanel</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
            </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">DataTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
        </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">ItemsControl.ItemTemplate</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">ItemsControl</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">

</span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">StackPanel</span><span style="color: #0000FF;">&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

This nifty XAML binding trick only works in WPF, unfortunately.&#160; Silverlight handles binding differently, so they don’t support binding to dynamic objects as of late (March 2010).&#160; This, in my opinion, is a major lacking feature in Silverlight and I **really** hope we will see this feature available to us in Silverlight 4 Release.&#160; (I am not very optimistic for Silverlight 4, but I can hope for the feature in Silverlight 5, can’t I?)

### Conclusion

I still have a few things I want to say about using YAML in the .Net space including de-serialization and using IronRuby for your YAML parser, but this post is hopefully enough to see how easy it is to incorporate YAML documents in your code.

[Codeplex Site for YAML tools](http://yaml.codeplex.com/ "http://yaml.codeplex.com/")

[Dynamic wrapper for YamlCore](http://pastie.org/890406)