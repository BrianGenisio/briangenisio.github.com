---
title: Adventures in Ruby MVVM – Bootstrapping Ruby
tags:
  - MVVM
  - Ruby
id: 141
categories:
  - Uncategorized
date: 2010-07-07 02:44:34
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fwww.houseofbilz.net%2F)](http://dotnetshoutout.com/Adventures-in-MVVM-Bootstrapping-Ruby)

In this post, I want to discuss how I am loading the ViewModels into the View.&#160; When I write my Views, I like to use the tools I have available to me; Visual Studio and/or Expression Blend.&#160; This means that I want a Visual Studio project and I want to be able to use these tools to create new views quickly.&#160; The XAML files that define my views will still be backed by the obligatory auto-generated C# code, but this is where I want my C# code to end (for these experiments).&#160; I don’t want to write an more C# code than that (with one exception).

My ViewModels, however, will be written in Ruby.&#160; I will use RSpec to specify these ViewModels and I will use an editor other than Visual Studio to edit the code; mostly because [Visual Studio does not have any tooling support for Ruby](http://connect.microsoft.com/VisualStudio/feedback/details/479957/ironruby-integration-in-visual-studio-2010)!&#160; At some point, my View world needs to converge with my ViewModel world.&#160; 

To support this, I have written the only C# code that I intend to write in these experiments.&#160; It is a bootstrapper, if you will, to load Ruby ViewModels.&#160; It looks like this:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:b592ec48-a83e-4b94-bb1a-7dda245b0f8e" class="wlWriterEditableSmartContent"><pre style="background-color:#FFFFFF;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> ViewModelLocator : DynamicObject
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> ScriptEngine engine;

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> ViewModelLocator()
    {
        engine </span><span style="color: #000000;">=</span><span style="color: #000000;"> IronRuby.Ruby.CreateEngine();
        engine.Runtime.LoadAssembly(</span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(INotifyPropertyChanged).Assembly);
        engine.Runtime.LoadAssembly(</span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(ICommand).Assembly);

        LoadAllViewModels();
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> LoadAllViewModels()
    {
        Directory
            .EnumerateFiles(</span><span style="color: #800000;">"</span><span style="color: #800000;">ViewModels</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">*.rb</span><span style="color: #800000;">"</span><span style="color: #000000;">).ToList()
            .ForEach(file </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> engine.ExecuteFile(file));
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">override</span><span style="color: #000000;"> </span><span style="color: #0000FF;">bool</span><span style="color: #000000;"> TryGetMember(GetMemberBinder binder, </span><span style="color: #0000FF;">out</span><span style="color: #000000;"> </span><span style="color: #0000FF;">object</span><span style="color: #000000;"> result)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (</span><span style="color: #0000FF;">base</span><span style="color: #000000;">.TryGetMember(binder, </span><span style="color: #0000FF;">out</span><span style="color: #000000;"> result))
            </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> </span><span style="color: #0000FF;">true</span><span style="color: #000000;">;

        result </span><span style="color: #000000;">=</span><span style="color: #000000;"> FindViewModel(binder.Name);

        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> result </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">;
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">object</span><span style="color: #000000;"> FindViewModel(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> name)
    {
        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> engine.Execute(name </span><span style="color: #000000;">+</span><span style="color: #000000;"> </span><span style="color: #800000;">"</span><span style="color: #800000;">.new</span><span style="color: #800000;">"</span><span style="color: #000000;">);
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

This **ViewModelLocator** class exists for one reason: to generate Ruby ViewModels.&#160; It creates a Ruby script engine and pre-loads all the ViewModels when it is constructed.&#160; It then sits around, waiting to create new ViewModels for the View:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:5b1c725b-3653-4271-b5be-07401c7d9818" class="wlWriterEditableSmartContent"><pre style="background-color:#FFFFFF;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">StackPanel </span><span style="color: #FF0000;">Orientation</span><span style="color: #0000FF;">="Horizontal"</span><span style="color: #FF0000;"> DataContext</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding PersonViewModel, Source={StaticResource VMLocator}}</span><span style="color: #0000FF;">"</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding first}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> Margin</span><span style="color: #0000FF;">="5,0"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">TextBlock </span><span style="color: #FF0000;">Text</span><span style="color: #0000FF;">="</span><span style="color: #808000;">{Binding last}</span><span style="color: #0000FF;">"</span><span style="color: #FF0000;"> Margin</span><span style="color: #0000FF;">="5,0"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
</span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">StackPanel</span><span style="color: #0000FF;">&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

In the XAML above, I am setting the DataContext of the StackPanel to create a new **PersonViewModel **(defined in Ruby). The binding references a **ViewModelLocator** as its source that was defined in App.xaml:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:98d3f3b4-1093-4864-8129-34ebdfa56bf3" class="wlWriterEditableSmartContent"><pre style="background-color:#FFFFFF;overflow: auto;"><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">Application.Resources</span><span style="color: #0000FF;">&gt;</span><span style="color: #000000;">
    </span><span style="color: #0000FF;">&lt;</span><span style="color: #800000;">IronRubyMVVM:ViewModelLocator </span><span style="color: #FF0000;">x:Key</span><span style="color: #0000FF;">="VMLocator"</span><span style="color: #FF0000;"> </span><span style="color: #0000FF;">/&gt;</span><span style="color: #000000;">
</span><span style="color: #0000FF;">&lt;/</span><span style="color: #800000;">Application.Resources</span><span style="color: #0000FF;">&gt;</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

A dead-simple ViewModel for illustration is defined in Ruby: 

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:441667ca-571c-48b6-92bb-2768760cc9d3" class="wlWriterEditableSmartContent"><pre style="background-color:#FFFFFF;overflow: auto;"><span style="color: #0000FF;">require</span><span style="color: #000000;"> File</span><span style="color: #000000;">.</span><span style="color: #000000;">dirname(</span><span style="color: #0000FF;">__FILE__</span><span style="color: #000000;">)  </span><span style="color: #000000;">+</span><span style="color: #000000;"> </span><span style="color: #000000; font-weight: bold;">"</span><span style="color: #000000; font-weight: bold;">/../RubyVM/ViewModelSupport</span><span style="color: #000000; font-weight: bold;">"</span><span style="color: #000000;">

class PersonViewModel
    include ViewModelSupport

    declare_notifiable </span><span style="color: #000000;">:</span><span style="color: #000000;">first</span><span style="color: #000000;">,</span><span style="color: #000000;"> </span><span style="color: #000000;">:</span><span style="color: #0000FF;">last</span><span style="color: #000000;">

    def initialize
        </span><span style="color: #800080;">@first</span><span style="color: #000000;"> </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #000000; font-weight: bold;">"</span><span style="color: #000000; font-weight: bold;">Brian</span><span style="color: #000000; font-weight: bold;">"</span><span style="color: #000000;">
        </span><span style="color: #800080;">@last</span><span style="color: #000000;"> </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #000000; font-weight: bold;">"</span><span style="color: #000000; font-weight: bold;">Genisio</span><span style="color: #000000; font-weight: bold;">"</span><span style="color: #000000;">
    end
end</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

You can see all the code in action from the [snapshot of my project from which I wrote this post](http://bitbucket.org/briangenisio/rubyvm/src/72b8541771ba/):&#160; I will be working on this project quite a bit after this post, so if you want to see what I have been working on, the [code is always available.](http://bitbucket.org/briangenisio/rubyvm)