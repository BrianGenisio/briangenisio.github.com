---
title: Adventures in Ruby MVVM – Firing Events from Ruby
tags:
  - MVVM
  - Ruby
  - Silverlight
  - WPF
id: 124
categories:
  - Uncategorized
date: 2010-06-14 13:34:45
---

#### 

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchives%2F2010%2F06%2F14%2Fadventures-in-ruby-mvvm-firing-events-from-ruby%2F)](http://dotnetshoutout.com/Adventures-in-Ruby-MVVM-Firing-Events-from-Ruby)

### My Experiment:

Can I move over to Ruby as my primary programming language when developing WPF and Silverlight applications?&#160; I have been playing around with what it would mean to use Ruby to write my ViewModels, which would also make it easier to incorporate models using Ruby.

My next few blog posts will revolve around some things I have learned with this experiment.&#160; As described in my disclaimer, I am a bit of a hack when it comes to Ruby.&#160; There are probably better ways to do what I am suggesting.&#160; If so, feel free to call me out.&#160; I won’t mind.&#160; Seriously. 

### INotifyPropertyChanged

So, if I am going to write ViewModels in Ruby, the first thing is to implement **INotifyPropertyChanged**, which is an interface that includes one event.&#160; It is used by WPF and SIlverlight to update the UI when something changes:
  <pre class="csharpcode"><span class="kwrd">public</span> <span class="kwrd">interface</span> INotifyPropertyChanged
{
    <span class="kwrd">event</span> PropertyChangedEventHandler PropertyChanged;
}</pre>
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

But… how do I define events from Ruby?&#160; It’s not like Ruby has events as a language feature.&#160; As it turns out, in C# for every event (Foo), two methods are created: **add_Foo()** and **remove_Foo()** which are mapped from **Foo += handler** and** Foo –= handler**.&#160; So, to get the **PropertyChanged** event methods implemented in Ruby, I just implement the **add_** and **remove_** methods.&#160; Following is the full implementation of a Ruby class that implements **INotifyPropertyChanged**:

<pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  <span style="color: #0000ff">class</span> TestClass
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    include System::ComponentModel::INotifyPropertyChanged  
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">def</span> initialize
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        @handlers = []
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">def</span> add_PropertyChanged(handler)
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        @handlers &lt;&lt; handler
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">def</span> remove_PropertyChanged(handler)
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        @handlers.delete(handler)
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">def</span> PropertyChanged(sender, args)
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        @handlers.each { |h| h.invoke(sender, args) }
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  <span style="color: #0000ff">end</span></pre></pre>

So, with that, the Ruby code can send an event to .Net code by calling **PropertyChanged()**.&#160; 

### But… Seriously… Lets Metaprogram, Shall We?

The previous code is kind of awkward.&#160; Every time you want to declare an event, you have to write this code?

Of course not.&#160; This is Ruby.&#160; We have metaprogramming!&#160; Lets create some code to generate this code.&#160; I talked with a ruby developer friend of mine, [Toby Tripp](http://twitter.com/tobytripp), and he helped me get to this mixin code that will generate the methods for you:

<pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">module</span> DotNetEvents
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  <span style="color: #0000ff">module</span> ClassMethods
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">def</span> declare_event( *events )
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      events.each <span style="color: #0000ff">do</span> |event|
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        define_handler_methods( event )
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">def</span> define_handler_methods( event )
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      event_name = event.to_s.<span style="color: #00008b">gsub</span>(/(?:^|_)(.)/) { $1.upcase }
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      class_eval &lt;&lt;-EOS
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">def</span> handlers( event_name )
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">          @event_handlers ||= [Hash](http://www.ruby-doc.org/docs/rdoc/1.9/classes/Hash.html).new
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">          @event_handlers[ event_name ] ||= []
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">def</span> add_<span style="color: #008000">#{event_name}( handler )</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">          handlers( :<span style="color: #008000">#{event_name} ) &lt;&lt; handler</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">def</span> remove_<span style="color: #008000">#{event_name}( handler )</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">          handlers( :<span style="color: #008000">#{event_name} ).delete handler</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">        <span style="color: #0000ff">def</span> <span style="color: #008000">#{event_name}( sender, args )</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">          handlers( :<span style="color: #008000">#{event_name} ).each do |handler|</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">            handler.invoke sender, args
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">          <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">      EOS
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  <span style="color: #0000ff">def</span> <span style="color: #0000ff">self</span>.included(receiver)
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">    receiver.extend ClassMethods
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  <span style="color: #0000ff">end</span>
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">end</span></pre></pre>

Now that we have this **declare_event** method, we can use it to define events.&#160; Just implement the interface, and declare the event:

<pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">class</span> TestClass
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  include DotNetEvents
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  include System::ComponentModel::INotifyPropertyChanged  
</pre><pre style="background-color: #ffffff; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px">  declare_event :PropertyChanged
</pre><pre style="background-color: #fbfbfb; margin: 0em; width: 100%; font-family: consolas,&#39;Courier New&#39;,courier,monospace; font-size: 12px"><span style="color: #0000ff">end</span></pre></pre>

Note that I have, indeed, tested this with WPF.&#160; The WPF system properly hooks up the event and when the Ruby code fires it, the UI gets updated.

From here, I can build my ViewModel base class in Ruby.&#160; Some things to solve still: making notification of PropertyChanged events easy, commands, data binding, ViewModel hookups, etc.&#160; But, at least, I am on my way :)