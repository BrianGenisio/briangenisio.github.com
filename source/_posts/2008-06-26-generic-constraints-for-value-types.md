---
title: Generic constraints for value types
id: 19
categories:
  - Uncategorized
date: 2008-06-26 13:55:00
tags:
---

I came across an oddity in the C# language the other day. I was trying to create a generic method that returned a nullable value. A simple example of this:
  <div style="border-bottom: gray 1px solid; border-left: gray 1px solid; padding-bottom: 4px; line-height: 12pt; background-color: #f4f4f4; margin: 20px 0px 10px; padding-left: 4px; width: 97.5%; padding-right: 4px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; max-height: 200px; font-size: 8pt; overflow: auto; border-top: gray 1px solid; cursor: text; border-right: gray 1px solid; padding-top: 4px">   <div style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px">     <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   1:</span> <span style="color: #0000ff">public</span> T? GetNullable&lt;T&gt;(<span style="color: #0000ff">int</span> index)</pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   2:</span> {</pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   3:</span>   <span style="color: #0000ff">if</span>(index &lt; 0)</pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   4:</span>       <span style="color: #0000ff">return</span> <span style="color: #0000ff">null</span>;</pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   5:</span>&#160; </pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   6:</span>   <span style="color: #0000ff">return</span> <span style="color: #0000ff">default</span>(T);</pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   7:</span> }</pre>
  </div>
</div>

<pre class="c-sharp:nogutter" name="code">&#160;</pre>

This will result in a compiler error:

Error 1 The type 'T' must be a non-nullable value type in order to use it as parameter 'T' in the generic type or method 'System.Nullable&lt;T&gt;' 

This is a cryptic error that means that I need to put a constraint on my method to restrict it to a value type. In C#, this is done with the 'struct' keyword:

<div style="border-bottom: gray 1px solid; border-left: gray 1px solid; padding-bottom: 4px; line-height: 12pt; background-color: #f4f4f4; margin: 20px 0px 10px; padding-left: 4px; width: 97.48%; padding-right: 4px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; height: 30px; max-height: 200px; font-size: 8pt; overflow: auto; border-top: gray 1px solid; cursor: text; border-right: gray 1px solid; padding-top: 4px">
  <div style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px">
    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   1:</span> <span style="color: #0000ff">public</span> T? GetNullable&lt;T&gt;(<span style="color: #0000ff">int</span> index) <span style="color: #0000ff">where</span> T : struct</pre>
  </div>
</div>

This seems really odd to me. If someone were to read the interface for this class, it is not at all clear that I can send an int, bool double, etc into this method. 

It turns out that T? and System.Nullable&lt;T&gt; compile into the same IL and the interface for System.Nullable&lt;T&gt; also has a constraint on 'struct'. So, as odd as it is, we need to consider the 'struct' constraint to really mean any value type.

Just a little bit later, I created a utility method to convert a string to an enum. The code is really straight forward:

<div style="border-bottom: gray 1px solid; border-left: gray 1px solid; padding-bottom: 4px; line-height: 12pt; background-color: #f4f4f4; margin: 20px 0px 10px; padding-left: 4px; width: 97.5%; padding-right: 4px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; max-height: 200px; font-size: 8pt; overflow: auto; border-top: gray 1px solid; cursor: text; border-right: gray 1px solid; padding-top: 4px">
  <div style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px">
    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   1:</span> <span style="color: #0000ff">public</span> T GetEnum&lt;T&gt;(<span style="color: #0000ff">string</span> enumValue)</pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   2:</span> {</pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   3:</span>    <span style="color: #0000ff">return</span> (T)Enum.Parse(<span style="color: #0000ff">typeof</span> (T), enumValue);</pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   4:</span> }</pre>
  </div>
</div>

Because this method is only designed to be used for enums, it would be great if I could add an enum constraint:

<div style="border-bottom: gray 1px solid; border-left: gray 1px solid; padding-bottom: 4px; line-height: 12pt; background-color: #f4f4f4; margin: 20px 0px 10px; padding-left: 4px; width: 97.29%; padding-right: 4px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; height: 33px; max-height: 200px; font-size: 8pt; overflow: auto; border-top: gray 1px solid; cursor: text; border-right: gray 1px solid; padding-top: 4px">
  <div style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px">
    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   1:</span> <span style="color: #0000ff">public</span> T GetEnum&lt;T&gt;(<span style="color: #0000ff">string</span> enumValue) <span style="color: #0000ff">where</span> T : enum</pre>
  </div>
</div>

This, unfortunately, is not valid. C# does, however, allow you to put a constraint with a base class. Since all enums are derived from System.Enum, I thought I could restrict using System.Enum: 

<div style="border-bottom: gray 1px solid; border-left: gray 1px solid; padding-bottom: 4px; line-height: 12pt; background-color: #f4f4f4; margin: 20px 0px 10px; padding-left: 4px; width: 97.5%; padding-right: 4px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; height: 32px; max-height: 200px; font-size: 8pt; overflow: auto; border-top: gray 1px solid; cursor: text; border-right: gray 1px solid; padding-top: 4px">
  <div style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px">
    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   1:</span> <span style="color: #0000ff">public</span> T GetEnum&lt;T&gt;(<span style="color: #0000ff">string</span> enumValue) <span style="color: #0000ff">where</span> T : System.Enum</pre>
  </div>
</div>

Again, no dice. System.Enum is considered a &quot;special class&quot; and cannot be used as a constraint. So, again, I am stuck using the 'struct' constraint and again, I am left feeling like this is not quite right. With this constraint, it would be perfectly acceptable to to write the following:

<div style="border-bottom: gray 1px solid; border-left: gray 1px solid; padding-bottom: 4px; line-height: 12pt; background-color: #f4f4f4; margin: 20px 0px 10px; padding-left: 4px; width: 97.5%; padding-right: 4px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; height: 31px; max-height: 200px; font-size: 8pt; overflow: auto; border-top: gray 1px solid; cursor: text; border-right: gray 1px solid; padding-top: 4px">
  <div style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px">
    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   1:</span> GetEnum&lt;<span style="color: #0000ff">int</span>&gt;(<span style="color: #006080">&quot;4&quot;</span>);</pre>
  </div>
</div>

Woof. I guess the only thing I can do now, is to add the following parameter check:

<div style="border-bottom: gray 1px solid; border-left: gray 1px solid; padding-bottom: 4px; line-height: 12pt; background-color: #f4f4f4; margin: 20px 0px 10px; padding-left: 4px; width: 97.5%; padding-right: 4px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; max-height: 200px; font-size: 8pt; overflow: auto; border-top: gray 1px solid; cursor: text; border-right: gray 1px solid; padding-top: 4px">
  <div style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px">
    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: white; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   1:</span> <span style="color: #0000ff">if</span>(!<span style="color: #0000ff">typeof</span>(T).IsEnum)</pre>

    <pre style="border-bottom-style: none; padding-bottom: 0px; line-height: 12pt; border-right-style: none; background-color: #f4f4f4; margin: 0em; padding-left: 0px; width: 100%; padding-right: 0px; font-family: consolas, &#39;Courier New&#39;, courier, monospace; border-top-style: none; color: black; font-size: 8pt; border-left-style: none; overflow: visible; padding-top: 0px"><span style="color: #606060">   2:</span>    <span style="color: #0000ff">throw</span> <span style="color: #0000ff">new</span> ArgumentException(<span style="color: #006080">&quot;Type T Must be an Enum&quot;</span>);</pre>
  </div>
</div>

This is the best I can do, thought it is far from ideal. It gives me a runtime check, at least, even though I can't get a compile-time check. 

It leaves me hoping that in C# 4.0, that we see some significant changes to generic constraints. It would be nice if we could at least have an Enum construct. In addition, it would be nice to use the 'value' keyword in constraints instead of 'struct'. It will only improve readability.