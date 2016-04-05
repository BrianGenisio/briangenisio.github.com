---
title: Adventures in Ruby MVVM – Wrapping it up
tags:
  - MVVM
  - Ruby
id: 153
categories:
  - Uncategorized
date: 2010-09-26 00:52:27
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchives%2F2010%2F09%2F26%2Fadventures-in-ruby-mvvm-wrapping-it-up%2F)](http://dotnetshoutout.com/Adventures-in-Ruby-MVVM-Wrapping-it-up)

[Source code for the RubyMVVM playground on BitBucket](http://bitbucket.org/briangenisio/rubyvm/src)

It has been quite a while since I blogged.&#160; Actually, I was just about to sit down to write this blog post about 7 weeks ago when my wife went into labor.&#160; Soon afterwards, she gave birth to our second child -- a son named [Eli Hecker Genisio](http://maia.genisio.org/2010/08/elis-birth.html).&#160; Needless to say, I have been neglecting blog for a while.

Previous to that, I had gotten to a place where I felt like I was ready to wrap up my experiments in using Ruby for ViewModels and Models.&#160; Here is what I would have written:

#### 

### What Worked, What Didn’t?

Using Ruby as the primary logic and model platform for MVVM (Presenter Model) style apps in WPF was a ton of fun.&#160; It gave me the chance to learn Ruby pretty well (I’m still a hack, but who isn’t?) and got to use many of the more advanced topics such as meta programming and mix-ins.&#160; My ViewModel code was succinct and my model code was even more trivial once I employed the [HTTParty](http://httparty.rubyforge.org/) gem.&#160; What was most fun (and likely my biggest take-away) was how wonderful writing tests using [rspec](http://rspec.info/) was.&#160; All of my tests were written using rspec and I think I might start writing my C# tests using rspec in the future.&#160; 

What didn’t work: The general coding workflow.&#160; Since there is no tooling support for IronRuby in VisualStudio 2010, it became a disjoined development effort.&#160; This was not completely bad, but things like debugging and deployment became difficult.&#160; I cringe when I think about deploying ruby gems with a Silverlight app. In addition ,the integration between .Net and IronRuby at runtime still needs work.&#160; For instance, defining anything but a trivial property in Ruby did not translate well into the .Net side.&#160; Also, WPF had difficulty binding to ruby strings.&#160; Some controls worked well, but some completely failed.&#160; I ended up writing a ValueConverter in WPF that called ToString() on the Ruby string in order to bind properly.&#160; Silverlight can’t even try to bind to a dynamic property, so that was even more difficult.

In all, I left feeling like the “goods” and “bads” canceled themselves out and I was stuck asking myself: “Why would I want to do this for real applications?”.&#160; My answer is: “Until there is good tooling support for IronRuby in Visual Studio (don’t hold your breath), this story probably doesn’t have any legs”.

### The Playground – A Twitter Search App

[![RubyMVVM_Twitter](http://houseofbilz.com/wp-content/uploads/2010/09/RubyMVVM_Twitter_thumb.png "RubyMVVM_Twitter")](http://houseofbilz.com/wp-content/uploads/2010/09/RubyMVVM_Twitter.png) 

With that, I’d like to document my playground in case anyone feels like running with it.&#160; I created a WPF Twitter Search application (the 2010 hello world) and hoste d the code on [BitBucket](http://bitbucket.org/briangenisio/rubyvm/src).&#160; Once I built all of the [mix-ins](http://bitbucket.org/briangenisio/rubyvm/src/tip/IronRubyMVVM/RubyVM/) necessary to support automatic property notification and convention-based command creation (amongst others), I came up with a ViewModel which is (mostly) void of .Net integration issues that looks like this:
  <div id="codeSnippetWrapper">   <div style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px" id="codeSnippet">     <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum1">   1:</span> class TwitterViewModel</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum2">   2:</span>   include ViewModelSupport</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum3">   3:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum4">   4:</span>   declare_notifiable :search_text, :responses</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum5">   5:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum6">   6:</span>   def responses</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum7">   7:</span>     @responses ||= NotifiableArray.new</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum8">   8:</span>   end</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum9">   9:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum10">  10:</span>   def execute_search</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum11">  11:</span>     responses.clear</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum12">  12:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum13">  13:</span>     Twitter.search(@search_text)[<span style="color: #006080">&quot;results&quot;</span>].each <span style="color: #0000ff">do</span> |tweet| </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum14">  14:</span>       responses.push(TweetViewModel.new(tweet)) </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum15">  15:</span>     end</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum16">  16:</span>   end</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum17">  17:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum18">  18:</span>   map_commands</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum19">  19:</span> end</pre>
<!--CRLF--></div>
</div>

1\. Notice that the very first thing I do is add the **ViewModelSupport** mixin.&#160; This provides the support for the magic that happens under the hood.&#160; 

2\. Immediately following, I use the **declare_notifiable** directive.&#160; This will create a property that will notify on change, using **INotifyPropertyChanged **in the .Net world.&#160; This was included with the mix-in.

3\. The **responses** method (read-only property in .Net) is a **NotifiableArray**. This is a Ruby array I built that will send collection changed events to .Net. 

4\. The **execute_search** method uses the **execute_** convention to generate an **ICommand** property named **search** that can be bound to in the view.&#160; This is what gets executed when the user presses the “Search Twitter” button.&#160; It goes off to Twitter (**Twitter.search** to be explained later) to search for the text entered into the **search_text** property.&#160; For each tweet that comes back, wrap it in a **TweetViewModel** (explained later) and put it into the **responses** collection.&#160; The View will update appropriately.

5\. The **map_commands** line is a class method that tells Ruby to go map any methods named **execute_*** to command properties (*) that can be bound to by the view.

As for the individual **TweetViewModel**, it looks like this:

<div id="codeSnippetWrapper">
  <div style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px" id="codeSnippet">
    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum1">   1:</span> class TweetViewModel</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum2">   2:</span>    include HashExposer</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum3">   3:</span>    expose :text, :profile_image_url, :created_at, :from_user</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum4">   4:</span> end</pre>
<!--CRLF--></div>
</div>

It uses **HashExposer** which is a mix-in I wrote that will find any items in the hash with the key declared with **expose** as a property.&#160; It is a very simple wrapper to allow for binding in the WPF view down to the individual tweet properties found in the hash.&#160; The hash that is passed in in the initializer is what these properties map to.

Finally, here is what the **Twitter** service looks like:

<div id="codeSnippetWrapper">
  <div style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px" id="codeSnippet">
    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum1">   1:</span> class Twitter</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum2">   2:</span>   include HTTParty</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum3">   3:</span>   base_uri 'search.twitter.com'</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum4">   4:</span>   format :json</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum5">   5:</span>   </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum6">   6:</span>   def self.search(query)</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum7">   7:</span>     get('/search.json', :query =&gt;<span style="color: #008000">; {:q =&gt; query})</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum8">   8:</span>   end</pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum9">   9:</span>     </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum10">  10:</span> end</pre>
<!--CRLF--></div>
</div>

By using the [HTTParty](http://httparty.rubyforge.org/) gem, I get to define what **Twitter.search(text)** looks like in a minimal way.&#160; The response of **search** is a parsed hash which I wrap with the **TweetViewModel**.&#160; This is an AMAZINGLY simple way to consume web services in Ruby.&#160; I was shocked to see how easy it was.&#160; 

That is everything on the Ruby side!&#160; All of the application-specific ruby code has been shown in this blog post.&#160; By using the **ViewModelSupport** mix-in (found in the source code), the rest of the .Net integration is abstracted away.&#160; With this alone, I felt like I was successful.&#160; 

Next, I’d like to show the WPF side of the application.&#160; This is what the view looks like:

<div style="border-bottom: silver 1px solid; text-align: left; border-left: silver 1px solid; padding-bottom: 4px; line-height: 12pt; background-color: #f4f4f4; margin: 20px 0px 10px; padding-left: 4px; width: 97.5%; padding-right: 4px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; max-height: 200px; font-size: 8pt; overflow: auto; border-top: silver 1px solid; cursor: text; border-right: silver 1px solid; padding-top: 4px" id="codeSnippetWrapper">
  <div style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px" id="codeSnippet">
    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum1">   1:</span> ﻿<span style="color: #0000ff">&lt;</span><span style="color: #800000">UserControl</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum2">   2:</span>     <span style="color: #ff0000">xmlns</span><span style="color: #0000ff">=&quot;http://schemas.microsoft.com/winfx/2006/xaml/presentation&quot;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum3">   3:</span>     <span style="color: #ff0000">xmlns:x</span><span style="color: #0000ff">=&quot;http://schemas.microsoft.com/winfx/2006/xaml&quot;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum4">   4:</span>     <span style="color: #ff0000">xmlns:d</span><span style="color: #0000ff">=&quot;http://schemas.microsoft.com/expression/blend/2008&quot;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum5">   5:</span>     <span style="color: #ff0000">xmlns:mc</span><span style="color: #0000ff">=&quot;http://schemas.openxmlformats.org/markup-compatibility/2006&quot;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum6">   6:</span>     <span style="color: #ff0000">xmlns:local</span><span style="color: #0000ff">=&quot;clr-namespace:IronRubyMVVM&quot;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum7">   7:</span>     <span style="color: #ff0000">xmlns:SampleData</span><span style="color: #0000ff">=&quot;clr-namespace:Expression.Blend.SampleData.Tweets&quot;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum8">   8:</span>     <span style="color: #ff0000">mc:Ignorable</span><span style="color: #0000ff">=&quot;d&quot;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum9">   9:</span>     <span style="color: #ff0000">x:Class</span><span style="color: #0000ff">=&quot;IronRubyMVVM.TwitterView&quot;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum10">  10:</span>     <span style="color: #ff0000">x:Name</span><span style="color: #0000ff">=&quot;UserControl&quot;</span> <span style="color: #ff0000">d:DesignHeight</span><span style="color: #0000ff">=&quot;651.96&quot;</span> <span style="color: #ff0000">d:DesignWidth</span><span style="color: #0000ff">=&quot;669&quot;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum11">  11:</span>     <span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum12">  12:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum13">  13:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">UserControl.Resources</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum14">  14:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">SampleData:Tweets</span> <span style="color: #ff0000">x:Key</span><span style="color: #0000ff">=&quot;Tweets&quot;</span> <span style="color: #ff0000">d:IsDataSource</span><span style="color: #0000ff">=&quot;True&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum15">  15:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">local:StringConverter</span> <span style="color: #ff0000">x:Key</span><span style="color: #0000ff">=&quot;ToString&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum16">  16:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">DataTemplate</span> <span style="color: #ff0000">x:Key</span><span style="color: #0000ff">=&quot;responsesItemTemplate1&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum17">  17:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">Border</span> <span style="color: #ff0000">BorderThickness</span><span style="color: #0000ff">=&quot;2&quot;</span> <span style="color: #ff0000">CornerRadius</span><span style="color: #0000ff">=&quot;4&quot;</span> <span style="color: #ff0000">BorderBrush</span><span style="color: #0000ff">=&quot;Black&quot;</span> <span style="color: #ff0000">Margin</span><span style="color: #0000ff">=&quot;3&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum18">  18:</span>                 <span style="color: #0000ff">&lt;</span><span style="color: #800000">Grid</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum19">  19:</span>                     <span style="color: #0000ff">&lt;</span><span style="color: #800000">Grid.ColumnDefinitions</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum20">  20:</span>                         <span style="color: #0000ff">&lt;</span><span style="color: #800000">ColumnDefinition</span> <span style="color: #ff0000">Width</span><span style="color: #0000ff">=&quot;Auto&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum21">  21:</span>                         <span style="color: #0000ff">&lt;</span><span style="color: #800000">ColumnDefinition</span> <span style="color: #ff0000">Width</span><span style="color: #0000ff">=&quot;*&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum22">  22:</span>                     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Grid.ColumnDefinitions</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum23">  23:</span>                     <span style="color: #0000ff">&lt;</span><span style="color: #800000">Grid.Background</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum24">  24:</span>                         <span style="color: #0000ff">&lt;</span><span style="color: #800000">LinearGradientBrush</span> <span style="color: #ff0000">EndPoint</span><span style="color: #0000ff">=&quot;0.5,1&quot;</span> <span style="color: #ff0000">StartPoint</span><span style="color: #0000ff">=&quot;0.5,0&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum25">  25:</span>                             <span style="color: #0000ff">&lt;</span><span style="color: #800000">GradientStop</span> <span style="color: #ff0000">Color</span><span style="color: #0000ff">=&quot;#FFD1EBFB&quot;</span> <span style="color: #ff0000">Offset</span><span style="color: #0000ff">=&quot;0.694&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum26">  26:</span>                             <span style="color: #0000ff">&lt;</span><span style="color: #800000">GradientStop</span> <span style="color: #ff0000">Color</span><span style="color: #0000ff">=&quot;White&quot;</span> <span style="color: #ff0000">Offset</span><span style="color: #0000ff">=&quot;0.116&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum27">  27:</span>                         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">LinearGradientBrush</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum28">  28:</span>                     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Grid.Background</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum29">  29:</span>                     <span style="color: #0000ff">&lt;</span><span style="color: #800000">StackPanel</span> <span style="color: #ff0000">Grid</span>.<span style="color: #ff0000">Column</span><span style="color: #0000ff">=&quot;0&quot;</span> <span style="color: #ff0000">Margin</span><span style="color: #0000ff">=&quot;2&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum30">  30:</span>                         <span style="color: #0000ff">&lt;</span><span style="color: #800000">Border</span> <span style="color: #ff0000">BorderBrush</span><span style="color: #0000ff">=&quot;Black&quot;</span> <span style="color: #ff0000">BorderThickness</span><span style="color: #0000ff">=&quot;3&quot;</span> <span style="color: #ff0000">Height</span><span style="color: #0000ff">=&quot;100&quot;</span> <span style="color: #ff0000">Width</span><span style="color: #0000ff">=&quot;100&quot;</span> <span style="color: #ff0000">CornerRadius</span><span style="color: #0000ff">=&quot;5&quot;</span> <span style="color: #ff0000">Margin</span><span style="color: #0000ff">=&quot;3&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum31">  31:</span>                             <span style="color: #0000ff">&lt;</span><span style="color: #800000">Image</span> <span style="color: #ff0000">Source</span><span style="color: #0000ff">=&quot;{Binding profile_image_url, Converter={StaticResource ToString}}&quot;</span> <span style="color: #ff0000">Margin</span><span style="color: #0000ff">=&quot;7&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum32">  32:</span>                         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Border</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum33">  33:</span>                         <span style="color: #0000ff">&lt;</span><span style="color: #800000">TextBlock</span> <span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;{Binding from_user}&quot;</span> <span style="color: #ff0000">HorizontalAlignment</span><span style="color: #0000ff">=&quot;Center&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum34">  34:</span>                         <span style="color: #0000ff">&lt;</span><span style="color: #800000">TextBlock</span> <span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;{Binding created_at}&quot;</span> <span style="color: #ff0000">HorizontalAlignment</span><span style="color: #0000ff">=&quot;Center&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum35">  35:</span>                     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">StackPanel</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum36">  36:</span>                     <span style="color: #0000ff">&lt;</span><span style="color: #800000">TextBlock</span> <span style="color: #ff0000">Grid</span>.<span style="color: #ff0000">Column</span><span style="color: #0000ff">=&quot;1&quot;</span> <span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;{Binding text}&quot;</span> <span style="color: #ff0000">VerticalAlignment</span><span style="color: #0000ff">=&quot;Top&quot;</span> <span style="color: #ff0000">TextWrapping</span><span style="color: #0000ff">=&quot;Wrap&quot;</span> <span style="color: #ff0000">Margin</span><span style="color: #0000ff">=&quot;8,30,0,0&quot;</span> <span style="color: #ff0000">FontSize</span><span style="color: #0000ff">=&quot;18.667&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum37">  37:</span>                 <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Grid</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum38">  38:</span>             <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Border</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum39">  39:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">DataTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum40">  40:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">ItemsPanelTemplate</span> <span style="color: #ff0000">x:Key</span><span style="color: #0000ff">=&quot;ItemsPanelTemplate1&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum41">  41:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">StackPanel</span> <span style="color: #ff0000">IsItemsHost</span><span style="color: #0000ff">=&quot;True&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum42">  42:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">ItemsPanelTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum43">  43:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">UserControl.Resources</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum44">  44:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">UserControl.Background</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum45">  45:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">LinearGradientBrush</span> <span style="color: #ff0000">EndPoint</span><span style="color: #0000ff">=&quot;0.5,1&quot;</span> <span style="color: #ff0000">StartPoint</span><span style="color: #0000ff">=&quot;0.5,0&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum46">  46:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">GradientStop</span> <span style="color: #ff0000">Color</span><span style="color: #0000ff">=&quot;#FFD6D6D6&quot;</span> <span style="color: #ff0000">Offset</span><span style="color: #0000ff">=&quot;0&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum47">  47:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">GradientStop</span> <span style="color: #ff0000">Color</span><span style="color: #0000ff">=&quot;White&quot;</span> <span style="color: #ff0000">Offset</span><span style="color: #0000ff">=&quot;1&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum48">  48:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">LinearGradientBrush</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum49">  49:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">UserControl.Background</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum50">  50:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum51">  51:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">UserControl.DataContext</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum52">  52:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">Binding</span> <span style="color: #ff0000">Path</span><span style="color: #0000ff">=&quot;TwitterViewModel&quot;</span> <span style="color: #ff0000">Source</span><span style="color: #0000ff">=&quot;{StaticResource VMLocator}&quot;</span><span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum53">  53:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">UserControl.DataContext</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum54">  54:</span>&#160; </pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum55">  55:</span>     <span style="color: #0000ff">&lt;</span><span style="color: #800000">Grid</span> <span style="color: #ff0000">x:Name</span><span style="color: #0000ff">=&quot;LayoutRoot&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum56">  56:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">Grid.RowDefinitions</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum57">  57:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">RowDefinition</span> <span style="color: #ff0000">Height</span><span style="color: #0000ff">=&quot;Auto&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum58">  58:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">RowDefinition</span> <span style="color: #ff0000">Height</span><span style="color: #0000ff">=&quot;*&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum59">  59:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Grid.RowDefinitions</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum60">  60:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">StackPanel</span> <span style="color: #ff0000">Orientation</span><span style="color: #0000ff">=&quot;Horizontal&quot;</span> <span style="color: #ff0000">Grid</span>.<span style="color: #ff0000">Row</span><span style="color: #0000ff">=&quot;0&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum61">  61:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">TextBox</span> <span style="color: #ff0000">Width</span><span style="color: #0000ff">=&quot;200&quot;</span> <span style="color: #ff0000">VerticalAlignment</span><span style="color: #0000ff">=&quot;Top&quot;</span> <span style="color: #ff0000">Text</span><span style="color: #0000ff">=&quot;{Binding search_text, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}&quot;</span> <span style="color: #ff0000">Margin</span><span style="color: #0000ff">=&quot;5,0&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum62">  62:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">Button</span> <span style="color: #ff0000">Content</span><span style="color: #0000ff">=&quot;Search Twitter&quot;</span> <span style="color: #ff0000">VerticalAlignment</span><span style="color: #0000ff">=&quot;Top&quot;</span> <span style="color: #ff0000">Command</span><span style="color: #0000ff">=&quot;{Binding search}&quot;</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum63">  63:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">StackPanel</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum64">  64:</span>         <span style="color: #0000ff">&lt;</span><span style="color: #800000">ItemsControl</span> <span style="color: #ff0000">Grid</span>.<span style="color: #ff0000">Row</span><span style="color: #0000ff">=&quot;1&quot;</span> <span style="color: #ff0000">ItemsSource</span><span style="color: #0000ff">=&quot;{Binding responses}&quot;</span> <span style="color: #ff0000">ItemTemplate</span><span style="color: #0000ff">=&quot;{DynamicResource responsesItemTemplate1}&quot;</span> <span style="color: #ff0000">d:DataContext</span><span style="color: #0000ff">=&quot;{Binding Source={StaticResource Tweets}}&quot;</span> <span style="color: #ff0000">HorizontalContentAlignment</span><span style="color: #0000ff">=&quot;Stretch&quot;</span> <span style="color: #ff0000">ItemsPanel</span><span style="color: #0000ff">=&quot;{DynamicResource ItemsPanelTemplate1}&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum65">  65:</span>             <span style="color: #0000ff">&lt;</span><span style="color: #800000">ItemsControl.Template</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum66">  66:</span>                 <span style="color: #0000ff">&lt;</span><span style="color: #800000">ControlTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum67">  67:</span>                     <span style="color: #0000ff">&lt;</span><span style="color: #800000">ScrollViewer</span> <span style="color: #ff0000">x:Name</span><span style="color: #0000ff">=&quot;ScrollViewer&quot;</span> <span style="color: #ff0000">Padding</span><span style="color: #0000ff">=&quot;{TemplateBinding Padding}&quot;</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum68">  68:</span>                         <span style="color: #0000ff">&lt;</span><span style="color: #800000">ItemsPresenter</span> <span style="color: #0000ff">/&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum69">  69:</span>                     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">ScrollViewer</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum70">  70:</span>                 <span style="color: #0000ff">&lt;/</span><span style="color: #800000">ControlTemplate</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum71">  71:</span>             <span style="color: #0000ff">&lt;/</span><span style="color: #800000">ItemsControl.Template</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum72">  72:</span>         <span style="color: #0000ff">&lt;/</span><span style="color: #800000">ItemsControl</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum73">  73:</span>     <span style="color: #0000ff">&lt;/</span><span style="color: #800000">Grid</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF-->

    <pre style="border-bottom-style: none; text-align: left; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: &#39;Courier New&#39;, courier, monospace; direction: ltr; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060" id="lnum74">  74:</span> <span style="color: #0000ff">&lt;/</span><span style="color: #800000">UserControl</span><span style="color: #0000ff">&gt;</span></pre>
<!--CRLF--></div>
</div>

Just as in any MVVM application, all of the connections between the View and the ViewModel happen through binding.&#160; This is no exception.

The only C# in this entire application are in the [bootstrapping of the ruby classes](http://houseofbilz.com/archives/2010/07/07/adventures-in-ruby-mvvm-bootstrapping-ruby/) (**ViewModelLocator**) and the **StringConverter** which helps bind the view to Ruby strings.

The code for these support classes can be found in the [source code on BitBucket](http://bitbucket.org/briangenisio/rubyvm/src).

This puts to rest my Ruby-based MVVM experiments.&#160; I learned a ton and I think there is a good foundation here for improvement if anyone wants to run with it. 

For me, I will be moving on to some posts about Flex and ActionScript – another learning project for me to experiment with.&#160; Stay tuned :)