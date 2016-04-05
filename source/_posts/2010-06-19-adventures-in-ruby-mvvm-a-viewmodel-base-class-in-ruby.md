---
title: Adventures in Ruby MVVM – A ViewModel Base Class in Ruby
tags:
  - MVVM
  - Ruby
  - Silverlight
  - WPF
id: 133
categories:
  - Uncategorized
date: 2010-06-19 02:52:03
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchives%2F2010%2F06%2F19%2Fadventures-in-ruby-mvvm-a-viewmodel-base-class-in-ruby%2F)](http://dotnetshoutout.com/Adventures-in-Ruby-MVVM-A-ViewModel-Base-Class-in-Ruby)

In [my last post](http://houseofbilz.com/archives/2010/06/14/adventures-in-ruby-mvvm-firing-events-from-ruby/), I talked about how to fire events from Ruby code such that .Net code can subscribe and receive them.&#160; I showed a simple implementation of INotifyPropertyChanged; the interface that is essential to MVVM development in WPF and Silverlight.

With that out of the way, I am moving forward to start building a simple ViewModel base class in Ruby that will let me declare notifiable properties with as little ceremony as possible.

### Background

Before I get started, I’d like to show what a simple notifiable property in C# looks like.&#160; If you are familiar with MVVM, this code is not new to you:
  <pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">private</span> <span style="color: #0000ff">string</span> first;
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">public</span> <span style="color: #0000ff">string</span> First
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">{
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">get</span> { <span style="color: #0000ff">return</span> first; }
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">set</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    {
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        first = <span style="color: #0000ff">value</span>;
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        OnPropertyChanged(&quot;<span style="color: #8b0000">First</span>&quot;);
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    }
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">}</pre></pre>

### The Same Thing In Ruby

<pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">def</span> first
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  @first
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">def</span> first=(val)
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  @first = val
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  on_property_changed :first
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">end</span></pre></pre>

### Generalizing

Already, the Ruby code is more readable (in my opinion).&#160; The cool thing is that we can do better – much, much better.&#160; 

By employing meta programming in the base class, these methods can be generated for us.&#160; The result is a ViewModel class that simply declares the properties as notifiable:

<pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">class</span> ViewModel &lt; ViewModelBase 
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  declare_notifiable :first, :last
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">end</span></pre></pre>

That’s it!&#160; Since the base class provides the property generation code, you can just declare that you want to create properties that are notifiable and you are ready to go.&#160; To make sure that the properties notify when they are set (and ultimately get updated in the View via data binding), make sure to call the methods (as opposed to the @fields):

<pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">def</span> update_values
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  <span style="color: #0000ff">self</span>.first = &quot;<span style="color: #8b0000">Brian</span>&quot;
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  <span style="color: #0000ff">self</span>.last = &quot;<span style="color: #8b0000">Genisio</span>&quot;
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">end</span></pre></pre>

### ViewModelBase code

Here is the base class that makes it all happen.&#160; For the DotNetEvents mixin code, see my [previous post](http://houseofbilz.com/archives/2010/06/14/adventures-in-ruby-mvvm-firing-events-from-ruby/).

<pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">class</span> ViewModelBase
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    include DotNetEvents
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    include System::ComponentModel::INotifyPropertyChanged
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    declare_event :PropertyChanged
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"></pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">def</span> <span style="color: #0000ff">self</span>.declare_notifiable( *symbols )
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      symbols.each <span style="color: #0000ff">do</span> | symbol |
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        define_notifiable_property( symbol )
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">def</span> <span style="color: #0000ff">self</span>.define_notifiable_property( property_name )
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      class_eval &lt;&lt;-EOS
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">def</span> <span style="color: #008000">#{property_name}</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">          @<span style="color: #008000">#{property_name}</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">def</span> <span style="color: #008000">#{property_name}=(val)</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">          @<span style="color: #008000">#{property_name} = val</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">          on_property_changed :<span style="color: #008000">#{property_name} </span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      EOS
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"></pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">def</span> on_property_changed(name)
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      PropertyChanged(<span style="color: #0000ff">self</span>, System::ComponentModel::PropertyChangedEventArgs.new(name))
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">end</span>
</pre></pre>

### Up Next…

Next, I will post about implementing commands in the ViewModel that the View can bind to.&#160; My goal is to get to a completely convention-based approach to commands, much like my C# [ViewModel Support](http://houseofbilz.com/archives/2010/05/08/adventures-in-mvvm-my-viewmodel-base/).&#160; Stay tuned.