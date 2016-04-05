---
title: Adventures in MVVM – My ViewModel Base
tags:
  - MVVM
  - Silverlight
  - VIew Model Support
  - WPF
id: 94
categories:
  - Uncategorized
date: 2010-05-08 14:02:00
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2010%2F05%2F08%2Fadventures-in-mvvm-ndash-my-viewmodel-base.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-My-ViewModel-Base) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2010%2f05%2f08%2fadventures-in-mvvm-ndash-my-viewmodel-base.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2010%2f05%2f08%2fadventures-in-mvvm-ndash-my-viewmodel-base.aspx)

First, I’d like to say: **THIS IS NOT A NEW MVVM FRAMEWORK.** I tend to believe that MVVM support code should be specific to the system you are building and the developers working on it.&#160; I have yet to find an MVVM framework that does everything I want it to without doing too much.&#160; Don’t get me wrong… there are some good frameworks out there.&#160; I just like to pick and choose things that make sense for me.&#160; As of Silveright 4, they don’t support binding to dynamic properties, so some of the capabilities are lost, but with a [little hacking](http://houseofbilz.com/archive/2010/05/14/adventures-in-mvvm-ndash-my-viewmodel-base-ndash-silverlight-support.aspx) we can make it work.

That being said, I want to share my ViewModel base class with the world.&#160; I have had several conversations with people about the problems I have solved using this ViewModel base.&#160; A while back, I posted an article about some experiments with a “[Rails Inspired ViewModel](http://geekswithblogs.net/HouseOfBilz/archive/2009/10/20/adventures-in-mvvm-ndash-a-rails-inspired-viewmodel.aspx)”.&#160; What followed from those ideas was a ViewModel base class that I take with me and use in my projects.&#160; It has a lot of features, all designed to reduce the friction in writing view models. I have put the code out on Codeplex under the project: [ViewModelSupport](http://viewmodelsupport.codeplex.com).

Finally, this article focuses on the ViewModel and only glosses over the View and the Model.&#160; Without all three, you don’t have MVVM.&#160; But this base class is for the ViewModel, so that is what I am focusing on.

**Features:**

1.  [Automatic Command Plumbing](#VMB.1)2.  [Property Change Notification](#VMB.2)3.  [Strongly Typed Property Getter/Setters](#VMB.3)4.  [Dynamic Properties](#VMB.4)5.  [Default Property values](#VMB.5)6.  [Derived Properties](#VMB.6)7.  [Automatic Method Execution](#VMB.7)8.  [Command CanExecute Change Notification](#VMB.8)9.  [Design-Time Detection](#VMB.9)10.  [What about Silverlight?](#VMB.10)  

#### Automatic Command Plumbing

This feature takes the plumbing out of creating commands.&#160; The common pattern for commands in a ViewModel is to have an Execute method as well as an optional CanExecute method.&#160; To plumb that together, you create an ICommand Property, and set it in the constructor like so:

**Before**
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:a6cfb050-745a-4872-9efc-942325c00278" class="wlWriterSmartContent">   <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> AutomaticCommandViewModel
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> AutomaticCommandViewModel()
    {
        MyCommand </span><span style="color: #000000">=</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000"> DelegateCommand(Execute_MyCommand, CanExecute_MyCommand);
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> Execute_MyCommand()
    {
        </span><span style="color: #008000">//</span><span style="color: #008000"> Do something</span><span style="color: #008000">
</span><span style="color: #000000">    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">bool</span><span style="color: #000000"> CanExecute_MyCommand()
    {
        </span><span style="color: #008000">//</span><span style="color: #008000"> Are we in a state to do something?</span><span style="color: #008000">
</span><span style="color: #000000">        </span><span style="color: #0000ff">return</span><span style="color: #000000"> </span><span style="color: #0000ff">true</span><span style="color: #000000">;
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> DelegateCommand MyCommand { </span><span style="color: #0000ff">get</span><span style="color: #000000">; </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">set</span><span style="color: #000000">; }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

With the base class, this plumbing is automatic and the property (MyCommand of type ICommand) is created for you.&#160; The base class uses the convention that methods be prefixed with Execute_ and CanExecute_ in order to be plumbed into commands with the property name after the prefix.&#160; You are left to be expressive with your behavior without the plumbing.&#160; If you are wondering how CanExecuteChanged is raised, see the later section “[Command CanExecute Change Notification](#VMB.8)”.

**After**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:14a1461b-d14f-47d5-ba10-5ad497c164be" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> AutomaticCommandViewModel : ViewModelBase
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> Execute_MyCommand()
    {
        </span><span style="color: #008000">//</span><span style="color: #008000"> Do something</span><span style="color: #008000">
</span><span style="color: #000000">    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">bool</span><span style="color: #000000"> CanExecute_MyCommand()
    {
        </span><span style="color: #008000">//</span><span style="color: #008000"> Are we in a state to do something?</span><span style="color: #008000">
</span><span style="color: #000000">        </span><span style="color: #0000ff">return</span><span style="color: #000000"> </span><span style="color: #0000ff">true</span><span style="color: #000000">;
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

#### &#160;

#### <a name="VMB.2">Property Change Notification</a>

One thing that always kills me when implementing ViewModels is how to make properties that notify when they change (via the INotifyPropertyChanged interface).&#160; There have been many attempts to make this more automatic.&#160; My base class includes one option.&#160; There are others, but I feel like this works best for me.

The common pattern (without my base class) is to create a private backing store for the variable and specify a getter that returns the private field.&#160; The setter will set the private field and fire an event that notifies the change, only if the value has changed.

**Before**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:3d02bc74-f4a8-4969-b4da-1dfa02a22ac3" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> PropertyHelpersViewModel : INotifyPropertyChanged
{
    </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> text;
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> Text
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> text; }
        </span><span style="color: #0000ff">set</span><span style="color: #000000">
        {
            </span><span style="color: #0000ff">if</span><span style="color: #000000">(text </span><span style="color: #000000">!=</span><span style="color: #000000"> value)
            {
                text </span><span style="color: #000000">=</span><span style="color: #000000"> value;
                RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Text</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
            }
        }
    }

    </span><span style="color: #0000ff">protected</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> RaisePropertyChanged(</span><span style="color: #0000ff">string</span><span style="color: #000000"> propertyName)
    {
        var handlers </span><span style="color: #000000">=</span><span style="color: #000000"> PropertyChanged;
        </span><span style="color: #0000ff">if</span><span style="color: #000000">(handlers </span><span style="color: #000000">!=</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
            handlers(</span><span style="color: #0000ff">this</span><span style="color: #000000">, </span><span style="color: #0000ff">new</span><span style="color: #000000"> PropertyChangedEventArgs(propertyName));
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">event</span><span style="color: #000000"> PropertyChangedEventHandler PropertyChanged;
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

This way of defining properties is error-prone and tedious.&#160; Too much plumbing.&#160; My base class eliminates much of that plumbing with the same functionality:

**After**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:12c2d3ea-f186-4de4-b580-4fb3f9d20391" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> PropertyHelpersViewModel : ViewModelBase
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> Text
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> Get</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">string</span><span style="color: #000000">&gt;</span><span style="color: #000000">(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Text</span><span style="color: #800000">&quot;</span><span style="color: #000000">); }
        </span><span style="color: #0000ff">set</span><span style="color: #000000"> { Set(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Text</span><span style="color: #800000">&quot;</span><span style="color: #000000">, value);}
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

#### 

#### &#160;

#### <a name="VMB.3">Strongly Typed Property Getters/Setters</a>

It turns out that we can do better than that.&#160; We are using a strongly typed language where the use of “Magic Strings” is often frowned upon.&#160; Lets make the names in the getters and setters strongly typed:

**A refinement**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:59293d3a-dbaa-4b14-a9b0-b64cc37fd4bd" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> PropertyHelpersViewModel : ViewModelBase
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> Text
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> Get(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Text); }
        </span><span style="color: #0000ff">set</span><span style="color: #000000"> { Set(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Text, value); }
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

#### &#160;

#### <a name="VMB.4">Dynamic Properties</a>

In C# 4.0, we have the ability to program statically OR dynamically.&#160; This base class lets us leverage the powerful dynamic capabilities in our ecosystem. (This is how the automatic commands are implemented, BTW)&#160; By calling Set(“Foo”, 1), you have now created a dynamic property called Foo.&#160; It can be bound against like any static property.&#160; The opportunities are endless.&#160; One great way to exploit this behavior is if you have a customizable view engine with templates that bind to properties defined by the user.&#160; The base class just needs to create the dynamic properties at runtime from information in the model, and the custom template can bind even though the static properties do not exist. All dynamic properties still benefit from the notifiable capabilities that static properties do.

For any nay-sayers out there that don’t like using the dynamic features of C#, just remember this: the act of binding the View to a ViewModel is dynamic already.&#160; Why not exploit it?&#160; Get over it :)

**Just declare the property dynamically**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:0af4d350-5601-4c6f-8bcc-78588703effe" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> DynamicPropertyViewModel : ViewModelBase
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> DynamicPropertyViewModel()
    {
        Set(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Foo</span><span style="color: #800000">&quot;</span><span style="color: #000000">, </span><span style="color: #800000">&quot;</span><span style="color: #800000">Bar</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

**Then reference it normally**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:b3f2f644-990c-4f03-96a4-87f382fd00f9" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">&lt;</span><span style="color: #800000">TextBlock </span><span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;</span><span style="color: #808000">{Binding Foo}</span><span style="color: #0000ff">&quot;</span><span style="color: #ff0000"> </span><span style="color: #0000ff">/&gt;</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

#### &#160;

#### <a name="VMB.5">Default Property Values</a>

The Get() method also allows for default properties to be set.&#160; Don’t set them in the constructor.&#160; Set them in the property and keep the related code together:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:c2dcd9b5-fd18-42da-84e4-c1ae7486ab03" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> Text
{
    </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> Get(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Text, </span><span style="color: #800000">&quot;</span><span style="color: #800000">This is the default value</span><span style="color: #800000">&quot;</span><span style="color: #000000">); }
    </span><span style="color: #0000ff">set</span><span style="color: #000000"> { Set(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Text, value);}
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

#### &#160;

#### <a name="VMB.6">Derived Properties</a>

This is something I [blogged about a while back](http://houseofbilz.com/archive/2009/11/14/adventures-in-mvvm----dependant-properties-with-inotifypropertychanged.aspx) in more detail.&#160; This feature came from the chaining of property notifications when one property affects the results of another, like this:

#### 

**Before**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:175eb2b2-8c26-430f-9416-944d4613c1a8" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> DependantPropertiesViewModel : ViewModelBase
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">double</span><span style="color: #000000"> Score
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> Get(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Score); }
        </span><span style="color: #0000ff">set</span><span style="color: #000000">
        {
            Set(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Score, value);
            RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Percentage</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
            RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Output</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
        }
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> Percentage
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> (</span><span style="color: #0000ff">int</span><span style="color: #000000">)(</span><span style="color: #800080">100</span><span style="color: #000000"> </span><span style="color: #000000">*</span><span style="color: #000000"> Score); }
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> Output
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> </span><span style="color: #800000">&quot;</span><span style="color: #800000">You scored </span><span style="color: #800000">&quot;</span><span style="color: #000000"> </span><span style="color: #000000">+</span><span style="color: #000000"> Percentage </span><span style="color: #000000">+</span><span style="color: #000000"> </span><span style="color: #800000">&quot;</span><span style="color: #800000">%.</span><span style="color: #800000">&quot;</span><span style="color: #000000">; }
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

The problem is: The setter for Score has to be responsible for notifying the world that Percentage and Output have also changed.&#160; This, to me, is backwards.&#160;&#160;&#160; It certainly violates the “[Single Responsibility Principle](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod).” I have been bitten in the rear more than once by problems created from code like this.&#160; What we really want to do is invert the dependency.&#160; Let the Percentage property declare that it changes when the Score Property changes.

**After**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:462bd99f-148f-4775-ae03-c6cf12f3fd98" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> DependantPropertiesViewModel : ViewModelBase
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">double</span><span style="color: #000000"> Score
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> Get(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Score); }
        </span><span style="color: #0000ff">set</span><span style="color: #000000"> { Set(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Score, value); }
    }

    [DependsUpon(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Score</span><span style="color: #800000">&quot;</span><span style="color: #000000">)]
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> Percentage
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> (</span><span style="color: #0000ff">int</span><span style="color: #000000">)(</span><span style="color: #800080">100</span><span style="color: #000000"> </span><span style="color: #000000">*</span><span style="color: #000000"> Score); }
    }

    [DependsUpon(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Percentage</span><span style="color: #800000">&quot;</span><span style="color: #000000">)]
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> Output
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> </span><span style="color: #800000">&quot;</span><span style="color: #800000">You scored </span><span style="color: #800000">&quot;</span><span style="color: #000000"> </span><span style="color: #000000">+</span><span style="color: #000000"> Percentage </span><span style="color: #000000">+</span><span style="color: #000000"> </span><span style="color: #800000">&quot;</span><span style="color: #800000">%.</span><span style="color: #800000">&quot;</span><span style="color: #000000">; }
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

#### &#160;

#### <a name="VMB.7">Automatic Method Execution</a>

This one is extremely similar to the previous, but it deals with method execution as opposed to property.&#160; When you want to execute a method triggered by property changes, let the method declare the dependency instead of the other way around.

**Before**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:96b9f4c0-884b-4ade-bb65-4b1954af6e4e" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> DependantMethodsViewModel : ViewModelBase
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">double</span><span style="color: #000000"> Score
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> Get(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Score); }
        </span><span style="color: #0000ff">set</span><span style="color: #000000">
        {
            Set(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Score, value);
            WhenScoreChanges();
        }
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> WhenScoreChanges()
    {
        </span><span style="color: #008000">//</span><span style="color: #008000"> Handle this case</span><span style="color: #008000">
</span><span style="color: #000000">    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

**After**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:b4314cc0-8387-4bb8-81db-e23c0cd1372c" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #000000">    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> DependantMethodsViewModel : ViewModelBase
    {
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">double</span><span style="color: #000000"> Score
        {
            </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> Get(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Score); }
            </span><span style="color: #0000ff">set</span><span style="color: #000000"> { Set(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Score, value); }
        }

        [DependsUpon(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Score</span><span style="color: #800000">&quot;</span><span style="color: #000000">)]
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> WhenScoreChanges()
        {
            </span><span style="color: #008000">//</span><span style="color: #008000"> Handle this case</span><span style="color: #008000">
</span><span style="color: #000000">        }
    }</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

#### &#160;

#### <a name="VMB.8">Command CanExecute Change Notification</a>

Back to Commands.&#160; One of the responsibilities of commands that implement ICommand – it must fire an event declaring that CanExecute() needs to be re-evaluated.&#160; I wanted to wait until we got past a few concepts before explaining this behavior.&#160; You can use the same mechanism here to fire off the change.&#160; In the CanExecute_ method, declare the property that it depends upon.&#160; When that property changes, the command will fire a CanExecuteChanged event, telling the View to re-evaluate the state of the command.&#160; The View will make appropriate adjustments, like disabling the button.

**DependsUpon works on CanExecute methods as well**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:81b91521-a967-4457-af0c-742b0b65c47c" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> CanExecuteViewModel : ViewModelBase
{
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> Execute_MakeLower()
    {
        Output </span><span style="color: #000000">=</span><span style="color: #000000"> Input.ToLower();
    }

    [DependsUpon(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Input</span><span style="color: #800000">&quot;</span><span style="color: #000000">)]
    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">bool</span><span style="color: #000000"> CanExecute_MakeLower()
    {
        </span><span style="color: #0000ff">return</span><span style="color: #000000"> </span><span style="color: #000000">!</span><span style="color: #0000ff">string</span><span style="color: #000000">.IsNullOrWhiteSpace(Input);
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> Input
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> Get(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Input); }
        </span><span style="color: #0000ff">set</span><span style="color: #000000"> { Set(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Input, value);}
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> Output
    {
        </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> Get(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Output); }
        </span><span style="color: #0000ff">set</span><span style="color: #000000"> { Set(() </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> Output, value); }
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

&#160;

#### <a name="VMB.9">Design-Time Detection</a>

If you want to add design-time data to your ViewModel, the base class has a property that lets you ask if you are in the designer.&#160; You can then set some default values that let your designer see what things might look like in runtime.

**Use the IsInDesignMode property**

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:1a20699e-0a10-4a8a-949c-c10753540af1" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> DependantPropertiesViewModel()
{
    </span><span style="color: #0000ff">if</span><span style="color: #000000">(IsInDesignMode)
    {
        Score </span><span style="color: #000000">=</span><span style="color: #000000"> .</span><span style="color: #800080">5</span><span style="color: #000000">;
    }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

#### &#160;

#### <a name="VMB.10">What About Silverlight?</a>

Although you cannot bind directly to dynamic properties and convention-based commands, you CAN bind using a value converter.&#160; This little hack is explained in more detail in my [next post](http://houseofbilz.com/archive/2010/05/14/adventures-in-mvvm-ndash-my-viewmodel-base-ndash-silverlight-support.aspx).&#160; Other than that slight difference, all of these features work in Silverlight just as they do in WPF.&#160; You don’t need to code your ViewModels any differently to get it to work, which aids in the sharing of behavior between WPF and Silverlight.

&#160;

#### Good to go?

So, that concludes the feature explanation of my ViewModel base class.&#160; Feel free to take it, fork it, whatever.&#160; It is [hosted on CodePlex](http://viewmodelsupport.codeplex.com).&#160; When I find other useful additions, I will add them to the public repository.&#160; I use this base class every day.&#160; It is mature, and well tested.&#160; If, however, you find any problems with it, please let me know!&#160; Also, feel free to suggest patches to me via the CodePlex site.&#160; :)