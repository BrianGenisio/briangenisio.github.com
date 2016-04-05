---
title: Adventures in MVVM – ViewModel Location and Creation
tags:
  - MVVM
  - ViewModel Support
id: 104
categories:
  - Uncategorized
date: 2010-06-04 12:34:00
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fwww.houseofbilz.com%2Farchive%2F2010%2F06%2F04%2Fadventures-in-mvvm-ndash-viewmodel-location-and-creation.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-ViewModel-Location-and-Creation)

In this post, I am going to explore how I prefer to attach ViewModels to my Views.&#160; I have published the code to my [ViewModelSupport project on CodePlex](http://viewmodelsupport.codeplex.com/) in case you'd like to see how it works along with some examples. 

#### 

#### Some History

My approach to View-First ViewModel creation has evolved over time.&#160; I have constructed ViewModels in code-behind.&#160; I have instantiated ViewModels in the resources sectoin of the view. I have used Prism to resolve ViewModels via Dependency Injection. I have created attached properties that use Dependency Injection containers underneath.&#160; Of all these approaches, I continue to find issues either in composability, blendability or maintainability. 

[Laurent Bugnion](http://www.galasoft.ch/index.html) came up with a pretty good approach in [MVVM Light Toolkit](http://mvvmlight.codeplex.com/) with his ViewModelLocator, but as [John Papa](http://johnpapa.net/) points out, it has maintenance issues.&#160; John paired up with [Glen Block](http://codebetter.com/blogs/glenn.block/) to make the ViewModelLocator more generic by [using MEF to compose ViewModels](http://johnpapa.net/silverlight/simple-viewmodel-locator-for-mvvm-the-patients-have-left-the-asylum/).&#160; It is a great approach, but I don’t like baking in specific resolution technologies into the [ViewModelSupport](http://viewmodelsupport.codeplex.com/) project.

I bring these people up, not to name drop, but to give them credit for the place I finally landed in my journey to resolve ViewModels.&#160; I have come up with my own version of the ViewModelLocator that is both generic and container agnostic.&#160; The solution is blendable, configurable and simple to use.&#160; Use any resolution mechanism you want: MEF, Unity, Ninject, Activator.Create, Lookup Tables, new, whatever.

#### How to use the locator

**1\. Create a class to contain your resolution configuration: **
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:53877bce-4af1-4fe5-bef3-afdd8e35dcc1" class="wlWriterSmartContent">   <pre style="background-color: #ffffff; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> YourViewModelResolver: IViewModelResolver
{
    </span><span style="color: #0000ff">private</span><span style="color: #000000"> YourFavoriteContainer container </span><span style="color: #000000">=</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> YourFavoriteContainer(); 

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> YourViewModelResolver()
    {
        </span><span style="color: #008000">//</span><span style="color: #008000"> Configure your container</span><span style="color: #008000">
</span><span style="color: #000000">    } 

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">object</span><span style="color: #000000"> Resolve(</span><span style="color: #0000ff">string</span><span style="color: #000000"> viewModelName)
    {
        </span><span style="color: #0000ff">return</span><span style="color: #000000"> container.Resolve(viewModelName);        
    }
} </span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>
<style type="text/css">
.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }</style>

Examples of doing this are on CodePlex for [MEF](http://viewmodelsupport.codeplex.com/SourceControl/changeset/view/47335#892520), [Unity](http://viewmodelsupport.codeplex.com/SourceControl/changeset/view/47335#892521) and [Activator.CreateInstance](http://viewmodelsupport.codeplex.com/SourceControl/changeset/view/47335#892522).

**2\. Create your ViewModelLocator with your custom resolver in App.xaml: **

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:6f6f210b-a908-4946-aa62-7f1b694d7376" class="wlWriterSmartContent">
  <pre style="background-color: #ffffff; overflow: auto"><span style="color: #0000ff">&lt;</span><span style="color: #800000">VMS:ViewModelLocator </span><span style="color: #ff0000">x:Key</span><span style="color: #0000ff">=&quot;ViewModelLocator&quot;</span><span style="color: #0000ff">&gt;</span><span style="color: #000000">
    </span><span style="color: #0000ff">&lt;</span><span style="color: #800000">VMS:ViewModelLocator.Resolver</span><span style="color: #0000ff">&gt;</span><span style="color: #000000">
        </span><span style="color: #0000ff">&lt;</span><span style="color: #800000">local:YourViewModelResolver </span><span style="color: #0000ff">/&gt;</span><span style="color: #000000">
    </span><span style="color: #0000ff">&lt;/</span><span style="color: #800000">VMS:ViewModelLocator.Resolver</span><span style="color: #0000ff">&gt;</span><span style="color: #000000">
</span><span style="color: #0000ff">&lt;/</span><span style="color: #800000">VMS:ViewModelLocator</span><span style="color: #0000ff">&gt;</span><span style="color: #000000"> </span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>
<style type="text/css">
.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }</style>

**3\. Hook up your data context whenever you want a ViewModel (WPF):**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:71e2aa4b-e6cd-4edd-ae0a-adb84db45363" class="wlWriterSmartContent">
  <pre style="background-color: #ffffff; overflow: auto"><span style="color: #0000ff">&lt;</span><span style="color: #800000">Border </span><span style="color: #ff0000">DataContext</span><span style="color: #0000ff">=&quot;</span><span style="color: #808000">{Binding YourViewModelName, Source={StaticResource ViewModelLocator}}</span><span style="color: #0000ff">&quot;</span><span style="color: #0000ff">&gt;</span><span style="color: #000000"> </span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>
<style type="text/css">

.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }</style>

This example uses dynamic properties on the ViewModelLocator and passes the name to your resolver to figure out how to compose it.

**4\. What about Silverlight? **

Good question.&#160; You can't bind to dynamic properties in Silverlight 4 (crossing my fingers for Silverlight 5), but you CAN use string indexing:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:f77d703c-fc69-4ff2-9ceb-40e6930b3d99" class="wlWriterSmartContent">
  <pre style="background-color: #ffffff; overflow: auto"><span style="color: #0000ff">&lt;</span><span style="color: #800000">Border </span><span style="color: #ff0000">DataContext</span><span style="color: #0000ff">=&quot;</span><span style="color: #808000">{Binding [YourViewModelName], Source={StaticResource ViewModelLocator}}</span><span style="color: #0000ff">&quot;</span><span style="color: #0000ff">&gt;</span><span style="color: #000000"> </span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>
<style type="text/css">

.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }</style>

But, as John Papa points out in his article, there is a silly bug in Silverlight 4 (as of this writing) that will call into the indexer 6 times when it binds.&#160; While this is little more than a nuisance when getting most properties, it can be much more of an issue when you are resolving ViewModels six times.&#160; If this gets in your way, the solution (as [pointed out](http://johnpapa.net/silverlight/simple-viewmodel-locator-for-mvvm-the-patients-have-left-the-asylum/) by John), is to use an IndexConverter (instantiated in App.xaml and also included in the project):

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:4e79efae-73d0-48b7-a376-8996fe872c08" class="wlWriterSmartContent">
  <pre style="background-color: #ffffff; overflow: auto"><span style="color: #0000ff">&lt;</span><span style="color: #800000">Border </span><span style="color: #ff0000">DataContext</span><span style="color: #0000ff">=&quot;</span><span style="color: #808000">{Binding Source={StaticResource ViewModelLocator}, 
    Converter={StaticResource IndexConverter}, ConverterParameter=YourViewModelName}</span><span style="color: #0000ff">&quot;</span><span style="color: #0000ff">&gt;</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>
<style type="text/css">

.csharpcode, .csharpcode pre
{
	font-size: small;
	color: black;
	font-family: consolas, "Courier New", courier, monospace;
	background-color: #ffffff;
	/*white-space: pre;*/
}
.csharpcode pre { margin: 0em; }
.csharpcode .rem { color: #008000; }
.csharpcode .kwrd { color: #0000ff; }
.csharpcode .str { color: #006080; }
.csharpcode .op { color: #0000c0; }
.csharpcode .preproc { color: #cc6633; }
.csharpcode .asp { background-color: #ffff00; }
.csharpcode .html { color: #800000; }
.csharpcode .attr { color: #ff0000; }
.csharpcode .alt 
{
	background-color: #f4f4f4;
	width: 100%;
	margin: 0em;
}
.csharpcode .lnum { color: #606060; }</style>

It is a bit uglier than the WPF version (this method will also work in WPF if you prefer), but it is still not all that bad. 

#### Conclusion

This approach works really well (I suppose I am a bit biased).&#160; It allows for composability from any mechanisim you choose.&#160; It is blendable (consider serving up different objects in Design Mode if you wish... or different constructors… whatever makes sense to you).&#160; It works in Cider.&#160; It is configurable.&#160; It is flexible.&#160; It is the best way I have found to manage View-First ViewModel hookups.&#160; Thanks to the guys mentioned in this article for getting me to something I love using.&#160; Enjoy.