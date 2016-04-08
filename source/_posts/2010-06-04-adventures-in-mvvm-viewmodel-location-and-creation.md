---
title: Adventures in MVVM – ViewModel Location and Creation
date: 2010-06-04 12:34:00
layout: post
category: Software
tags: [MVVM, ViewModel Support]
permalink: /archives/2010/06/04/adventures-in-mvvm-viewmodel-location-and-creation/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/)

In this post, I am going to explore how I prefer to attach ViewModels to my Views.&#160; I have published the code to my [ViewModelSupport project on CodePlex](http://viewmodelsupport.codeplex.com/) in case you'd like to see how it works along with some examples. 

#### Some History

My approach to View-First ViewModel creation has evolved over time.&#160; I have constructed ViewModels in code-behind.&#160; I have instantiated ViewModels in the resources sectoin of the view. I have used Prism to resolve ViewModels via Dependency Injection. I have created attached properties that use Dependency Injection containers underneath.&#160; Of all these approaches, I continue to find issues either in composability, blendability or maintainability. 

[Laurent Bugnion](http://www.galasoft.ch/index.html) came up with a pretty good approach in [MVVM Light Toolkit](http://mvvmlight.codeplex.com/) with his ViewModelLocator, but as [John Papa](http://johnpapa.net/) points out, it has maintenance issues.&#160; John paired up with [Glen Block](http://codebetter.com/blogs/glenn.block/) to make the ViewModelLocator more generic by [using MEF to compose ViewModels](http://johnpapa.net/silverlight/simple-viewmodel-locator-for-mvvm-the-patients-have-left-the-asylum/).&#160; It is a great approach, but I don’t like baking in specific resolution technologies into the [ViewModelSupport](http://viewmodelsupport.codeplex.com/) project.

I bring these people up, not to name drop, but to give them credit for the place I finally landed in my journey to resolve ViewModels.&#160; I have come up with my own version of the ViewModelLocator that is both generic and container agnostic.&#160; The solution is blendable, configurable and simple to use.&#160; Use any resolution mechanism you want: MEF, Unity, Ninject, Activator.Create, Lookup Tables, new, whatever.

#### How to use the locator

**1\. Create a class to contain your resolution configuration: **
```csharp
public class YourViewModelResolver: IViewModelResolver
{
    private YourFavoriteContainer container = new YourFavoriteContainer(); 

    public YourViewModelResolver()
    {
        // Configure your container
    } 

    public object Resolve(string viewModelName)
    {
        return container.Resolve(viewModelName);
    }
} 
```

 
Examples of doing this are on CodePlex for [MEF](http://viewmodelsupport.codeplex.com/SourceControl/changeset/view/47335#892520), [Unity](http://viewmodelsupport.codeplex.com/SourceControl/changeset/view/47335#892521) and [Activator.CreateInstance](http://viewmodelsupport.codeplex.com/SourceControl/changeset/view/47335#892522).

**2\. Create your ViewModelLocator with your custom resolver in App.xaml: **

```xml
<VMS:ViewModelLocator x:Key="ViewModelLocator">
    <VMS:ViewModelLocator.Resolver>
        <local:YourViewModelResolver />
    </VMS:ViewModelLocator.Resolver>
</VMS:ViewModelLocator> 
```

**3\. Hook up your data context whenever you want a ViewModel (WPF):**

```xml
<Border DataContext="{Binding YourViewModelName, Source={StaticResource ViewModelLocator}}"> 
```

This example uses dynamic properties on the ViewModelLocator and passes the name to your resolver to figure out how to compose it.

**4\. What about Silverlight? **

Good question.&#160; You can't bind to dynamic properties in Silverlight 4 (crossing my fingers for Silverlight 5), but you CAN use string indexing:

```xml
<Border DataContext="{Binding [YourViewModelName], Source={StaticResource ViewModelLocator}}"> 
```

But, as John Papa points out in his article, there is a silly bug in Silverlight 4 (as of this writing) that will call into the indexer 6 times when it binds.&#160; While this is little more than a nuisance when getting most properties, it can be much more of an issue when you are resolving ViewModels six times.&#160; If this gets in your way, the solution (as [pointed out](http://johnpapa.net/silverlight/simple-viewmodel-locator-for-mvvm-the-patients-have-left-the-asylum/) by John), is to use an IndexConverter (instantiated in App.xaml and also included in the project):

```xml
<Border DataContext="{Binding Source={StaticResource ViewModelLocator},
    Converter={StaticResource IndexConverter}, ConverterParameter=YourViewModelName}">
```

It is a bit uglier than the WPF version (this method will also work in WPF if you prefer), but it is still not all that bad. 

#### Conclusion

This approach works really well (I suppose I am a bit biased).&#160; It allows for composability from any mechanisim you choose.&#160; It is blendable (consider serving up different objects in Design Mode if you wish... or different constructors… whatever makes sense to you).&#160; It works in Cider.&#160; It is configurable.&#160; It is flexible.&#160; It is the best way I have found to manage View-First ViewModel hookups.&#160; Thanks to the guys mentioned in this article for getting me to something I love using.&#160; Enjoy.