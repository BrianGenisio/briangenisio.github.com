---
title: Adventures in MVVM – A Rails-Inspired ViewModel
id: 79
categories:
  - Uncategorized
date: 2009-10-20 13:52:00
tags:
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fgeekswithblogs.net%2FHouseOfBilz%2Farchive%2F2009%2F10%2F20%2Fadventures-in-mvvm-ndash-a-rails-inspired-viewmodel.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-A-Rails-Inspired-ViewModel) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2009%2f10%2f20%2fadventures-in-mvvm-ndash-a-rails-inspired-viewmodel.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2009%2f10%2f20%2fadventures-in-mvvm-ndash-a-rails-inspired-viewmodel.aspx)

I have been taking a few weeks off from my personal studies/experiments in the .Net space to learn more about Ruby and Ruby on Rails (RoR).&#160; I have two main goals for this study:

1.  Get to know another platform as an option for future development2.  Examine how I can incorporate different concepts into my existing framework  

_What follows is intended to document my experimentation with the second bullet:&#160; How might we design a ViewModel if we were in Rails?&#160; My thoughts on this are still forming, and I am still a novice in the RoR arena, so cut me some slack :)._

I am pretty vocal about my thoughts on the MVVM _pattern_.&#160; I really like the pattern a lot and I think it solves a lot of problems that I encounter on a daily basis.&#160; Unfortunately, the implementation of the pattern – ViewModels specifically – often get overly complicated.&#160; This is why there are about a dozen major players in this arena.&#160; The plumbing necessary to create a good ViewModel is tedious and error prone.&#160; A good set of abstractions and tools make the job much easier.

The RoR approach to application development uses a lot of conventions to allow the developer to be as expressive as possible without introducing too much plumbing.&#160; The framework facilitates DRY (Don’t Repeat Yourself) so your controllers declare what they do and the magic under the hood plumbs it all together for you.&#160; How can we translate these ideas to a better implementation of the MVVM pattern?&#160; In this article, I will focus on the ViewModel portion of the MVVM pattern.

### 

### A classic ViewModel implementation

This view is simple.&#160; There is a text box bound to the **Text** property in the ViewModel and a button bound to the **MakeLowercase** command (property) in the ViewModel.&#160; The button is enabled/disabled based on the length of the **Text** property and clicking on the button will cause the text to go to lowercase.
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:80acc5b9-9793-4bbd-9ced-93a4738c0d06" class="wlWriterSmartContent">   <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">&lt;</span><span style="color: #800000">TextBox </span><span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;</span><span style="color: #808000">{Binding Text, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}</span><span style="color: #0000ff">&quot;</span><span style="color: #ff0000"> Grid.Column</span><span style="color: #0000ff">=&quot;0&quot;</span><span style="color: #ff0000"> </span><span style="color: #0000ff">/&gt;</span><span style="color: #000000">
</span><span style="color: #0000ff">&lt;</span><span style="color: #800000">Button </span><span style="color: #ff0000">Content</span><span style="color: #0000ff">=&quot;Make Lower&quot;</span><span style="color: #ff0000"> Commands:Click.Command</span><span style="color: #0000ff">=&quot;</span><span style="color: #808000">{Binding MakeLowercase}</span><span style="color: #0000ff">&quot;</span><span style="color: #ff0000"> Grid.Column</span><span style="color: #0000ff">=&quot;1&quot;</span><span style="color: #ff0000"> </span><span style="color: #0000ff">/&gt;</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

Next is the ViewModel in its pure (no abstractions) form:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:2390ca1b-8c21-4df9-ada0-20d9a5627c2d" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> MainPageViewModel : INotifyPropertyChanged
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> MainPageViewModel()
    {
        MakeLowercase </span><span style="color: #000000">=</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> DelegateCommand</span><span style="color: #000000">&lt;</span><span style="color: #000000">pobject</span><span style="color: #000000">&gt;</span><span style="color: #000000">(ExecuteMakeLowercase, CanExecuteMakeLowercase);
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> ExecuteMakeLowercase(</span><span style="color: #0000ff">object</span><span style="color: #000000"> obj)
    {
        Text </span><span style="color: #000000">=</span><span style="color: #000000"> Text.ToLower();
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">bool</span><span style="color: #000000"> CanExecuteMakeLowercase(</span><span style="color: #0000ff">object</span><span style="color: #000000"> arg)
    {
        </span><span style="color: #0000ff">return</span><span style="color: #000000"> </span><span style="color: #000000">!</span><span style="color: #0000ff">string</span><span style="color: #000000">.IsNullOrEmpty(Text);
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> _text;
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> Text
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> _text; }
        </span><span style="color: #0000ff">set</span><span style="color: #000000">
        {
            _text </span><span style="color: #000000">=</span><span style="color: #000000"> value;
            FirePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Text</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
            MakeLowercase.RaiseCanExecuteChanged();
        }
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> DelegateCommand</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">object</span><span style="color: #000000">&gt;</span><span style="color: #000000"> _makeLowercase;
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> DelegateCommand</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">object</span><span style="color: #000000">&gt;</span><span style="color: #000000"> MakeLowercase
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> _makeLowercase; }
        </span><span style="color: #0000ff">set</span><span style="color: #000000">
        {
            _makeLowercase </span><span style="color: #000000">=</span><span style="color: #000000"> value;
            FirePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">MakeLowercase</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
        }
    }

    </span><span style="color: #0000ff">protected</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> FirePropertyChanged(</span><span style="color: #0000ff">string</span><span style="color: #000000"> propertyName)
    {
        var handlers </span><span style="color: #000000">=</span><span style="color: #000000"> PropertyChanged;
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (handlers </span><span style="color: #000000">!=</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
            handlers(</span><span style="color: #0000ff">this</span><span style="color: #000000">, </span><span style="color: #0000ff">new</span><span style="color: #000000"> PropertyChangedEventArgs(propertyName));
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">event</span><span style="color: #000000"> PropertyChangedEventHandler PropertyChanged;
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

### Problems with the traditional ViewModel implementation

1.  Lots of plumbing (Command in the constructor, PropertyChanged in property setters, ExecuteChanged evaluation in the **Text** setter, etc)2.  Verbose properties (We can’t use auto-properties because of the need to notify of changes3.  Unrelated events (**Text** property needs to know that **CanExecuteMakeLowercase** needs to be re-evaluated)4.  INotifyPropertyChanged is error-prone due to string events5.  Lots of redundancies makes maintenance messy

### What a Rails-Inspired ViewModel might look like

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:140cd01f-44d3-40b5-8072-90229fa0c901" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> MainPageViewModel : ActiveViewModel</span><span style="color: #000000">&lt;</span><span style="color: #000000">MainPageViewModel.Properties</span><span style="color: #000000">&gt;</span><span style="color: #000000">
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">enum</span><span style="color: #000000"> Properties { Text }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> Execute_MakeLowercase()
    {
        Set(Properties.Text, Get</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">string</span><span style="color: #000000">&gt;</span><span style="color: #000000">(Properties.Text).ToLower());
    }

    [DependentUpon(Properties.Text)]
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">bool</span><span style="color: #000000"> CanExecute_MakeLowercase()
    {
        </span><span style="color: #0000ff">return</span><span style="color: #000000"> </span><span style="color: #000000">!</span><span style="color: #0000ff">string</span><span style="color: #000000">.IsNullOrEmpty(Get</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">string</span><span style="color: #000000">&gt;</span><span style="color: #000000">(Properties.Text));
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

My goal was to reduce the amount of plumbing and redundancies in this code to communicate only what it needs to

1.  There is one property named **Text**2.  The **MakeLowercase** command will act on the **Text** property and make it lowercase3.  **CanExecuteMakeLowercase** will return true if **Text** has meaningful data4.  **CanExecuteMakeLowercase** needs to be re-evaluated with **Text** changes

There is a lot of magic in **ActiveViewModel.&#160; **I am using convention to define the properties as a list of enums (**Properties**).&#160; The ActiveViewModel will generate these properties dynamically.&#160; I am also using convention to create commands.&#160; Any method that is prefixed with **Execute_** will be made into a command property.&#160; Any method prefixed with **CanExecute_** will be evaluated in the same command.&#160; Further, the **CanExecute_** method can declare the properties that will cause the commands to re-evaluate, putting the dependency where it belongs – with the method that is dependant upon it!&#160; Testability is really easy: Call the actions and validate the properties (via public Get()).&#160; I didn’t implement it, but actions based on property setters could very easily use the **DependentUpon** attribute to avoid **INotifyPropertyChanged** chaining that can get quite ugly.

### Summary

I think this approach has a lot of potential.&#160; I am posting it in order to move the conversation forward.&#160; When the MVVM dust settles, and we have some best practices flushed out, what do we want our ViewModels to look like?&#160; Even though I consider this class experimental and exploratory (not meant to be used in production), I feel compelled to call out some weaknesses that will (or may) need to be addressed for a more production-ready base class:

1.  The **ActiveViewModel** class will not work in Silverlight.&#160; It relies heavily on custom type descriptors which are not available in Silverlight.&#160; Hopefully, in Silverlight 4.0, we will have some better support for dynamic property generation.&#160; This may be in the form of C# 4.0 (with the DynamicObject.Try[SG]etMember overrides, for instance), property descriptors, or something else (better IronRuby support, perhaps?)2.  There is not a lot of type safety with the properties.&#160; They are stored internally as plain objects.&#160; The **Get** method lets you get at a type-casted value, but you need to know it when you ask for it.&#160; We might change the property declaration from an enum to a list of name/type pairs and enforce some better type safety in the base class?3.  Because there are not properties, we loose “equals semantics”.&#160; Get/Set is harder to read/follow than simply assigning and reading properties

Finally, in case you are curious, you can see my implementation (at the time of this post) of the **ActiveViewModel** from my [Launchpad project](http://bazaar.launchpad.net/~briangenisio/mypetproject/Main/annotate/7/ActiveViewModel/ActiveViewModel.cs).