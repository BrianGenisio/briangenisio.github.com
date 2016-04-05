---
title: What to do About Those Pesky Singletons?
id: 65
categories:
  - Uncategorized
date: 2009-05-02 17:56:00
tags:
---

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fwww.houseofbilz.com%2Farchive%2F2009%2F05%2F02%2Fwhat-to-do-about-those-pesky-singletons.aspx)](http://dotnetshoutout.com/What-to-do-About-Those-Pesky-Singletons) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f05%2f02%2fwhat-to-do-about-those-pesky-singletons.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fwww.houseofbilz.com%2farchive%2f2009%2f05%2f02%2fwhat-to-do-about-those-pesky-singletons.aspx)   

No matter what your opinion is about singletons (I favor against them in most cases), it is important to understand what you can do when you encounter code that is dependent upon them.&#160; Unfortunately, I have come across a lot of code that has abused singletons in a way that makes other code less testable.&#160; My examples here are in C#, but this technique can work in any language.&#160; In some languages, these techniques are easier due to their dynamic capabilities.

I am going to choose a singleton example I have seen in books when the “Singleton Pattern is described – a logger.&#160; Have you seen code like this before?
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:34c33bf7-f0b0-498c-aaf2-8f47830ca230" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> Log
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> Log()
    {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Set up a way to write to a logging service somewhere</span><span style="color: #008000;">
</span><span style="color: #000000;">    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> Log theOnlyOne </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Log();
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> Log Instance
    {
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;"> { </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> theOnlyOne; }
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> WriteToLog(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> errorLevel, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message)
    {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Write to the logging service</span><span style="color: #008000;">
</span><span style="color: #000000;">    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Error(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message)
    {
        WriteToLog(</span><span style="color: #800000;">"</span><span style="color: #800000;">ERROR</span><span style="color: #800000;">"</span><span style="color: #000000;">, category, message);
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Warning(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message)
    {
        WriteToLog(</span><span style="color: #800000;">"</span><span style="color: #800000;">WARNING</span><span style="color: #800000;">"</span><span style="color: #000000;">, category, message);
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Information(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message)
    {
        WriteToLog(</span><span style="color: #800000;">"</span><span style="color: #800000;">INFORMATION</span><span style="color: #800000;">"</span><span style="color: #000000;">, category, message);
    }
}</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> SomeClass
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> Calculate()
    {
        Log.Instance.Information(</span><span style="color: #800000;">"</span><span style="color: #800000;">client</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Doing something...</span><span style="color: #800000;">"</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> do calculations</span><span style="color: #008000;">
</span><span style="color: #000000;">        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> </span><span style="color: #800080;">0</span><span style="color: #000000;">;
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

**Disclaimer: **I am not addressing concurrency issues at all in this post.&#160; These techniques work with or without concurrency code.&#160; For the sake of brevity, I left it out.

Of course, if I want to unit-test Calculate(), I have a problem.&#160; Every time I run my test(s), the event log will get written to.&#160; This could be a file, a database, or even a web service.&#160; This type of side-effect behavior is a pretty bad way to be writing my unit tests.&#160; To make matters worse, what happens if I want to validate that Calculate() logged a message appropriately?&#160; Do I read the file, database or web service to validate?&#160; 

What I really want here is a test double… but how do I get it in there?&#160; I can:

1.  Re-factor out the singleton2.  Re-factor the consumer3.  Put a shim in it4.  Wrap it

In C#, the first three methods require that the Log be modified.&#160; It either needs to implement an interface, or the methods need to be made virtual.&#160; I prefer to use an interface so that none of the base implementation makes it into the test.&#160; The fourth method can be used in a case where the ability to modify the singleton is prohibited for one reason or another. 

The interface for all four strategies is:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:2cbca8b4-f7c0-4990-9ce2-afd17a895758" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">interface</span><span style="color: #000000;"> ILog
{
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Error(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message);
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Warning(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message);
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Information(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message);
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### Re-Factor Out the Singleton

It is often the case that a singleton does not need to be a singleton. It was just a lazy design decision made a long time ago, but it doesn’t need to be this way.&#160; In fact, when it makes sense to do so, this is my preferred way to deal with singletons.&#160; In this case, I have to ask: why does it need to be a singleton?&#160; There might be a reason, but in most cases, I can just modify the class to not be a singleton anymore and modify the consumer to accept the ILog object via dependency injection:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:34188552-ad7a-4f91-b4bc-49804c54329a" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> Log : ILog
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> Log()
    {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Set up a way to write to a logging service somewhere</span><span style="color: #008000;">
</span><span style="color: #000000;">    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Remove the static instance
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Remove the Instance getter

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Everything else the same</span><span style="color: #008000;">
</span><span style="color: #000000;">}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:12883bea-208e-4edf-99d3-2160a049986b" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> SomeClass
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> ILog log;
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> SomeClass(ILog log)
    {
        </span><span style="color: #0000FF;">this</span><span style="color: #000000;">.log </span><span style="color: #000000;">=</span><span style="color: #000000;"> log;
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> SomeClass() : </span><span style="color: #0000FF;">this</span><span style="color: #000000;">(</span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Log())
    {}

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> Calculate()
    {
        log.Information(</span><span style="color: #800000;">"</span><span style="color: #800000;">client</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Doing something...</span><span style="color: #800000;">"</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> do calculations</span><span style="color: #008000;">
</span><span style="color: #000000;">        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> </span><span style="color: #800080;">0</span><span style="color: #000000;">;
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Now the test is easy to write.&#160; I can create a test double of any type I want here, just as long as it implements ILog.

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:17d72c01-afef-4625-8ab7-3f1fe613b41b" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">[TestFixture]
</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> SomeClassTests
{
    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_That_Calculate_Returns_Zero()
    {
        var test </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> SomeClass(GetLoggerStub());

        Assert.That(test.Calculate(), Is.EqualTo(</span><span style="color: #800080;">0</span><span style="color: #000000;">));
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

That is the best way to work with a singleton, in my opinion.&#160; Unfortunately, it is not always that easy.&#160; Maybe it is too much of a refactoring to take the “single” out of the singleton.&#160; Whatever the reason, I need the singleton to stay as a singleton.&#160; The next question:&#160; Do I need my consumer to treat it like a singleton?

### 

### Re-factor the Consumer

In this case, the Log class will stay as a singleton, but it is modified slightly to implement ILog:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:708132fc-c4cd-42a1-90de-05fe08260175" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> Log : ILog
{
    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Everything else the same</span><span style="color: #008000;">
</span><span style="color: #000000;">}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Next, the consumer is modified in a similar way to pass the ILog object in via dependency injection:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:91a73132-a87c-4e96-9d26-99f8990392b2" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> SomeClass
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> Func</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">ILog</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> log;
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> SomeClass(Func</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">ILog</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> log)
    {
        </span><span style="color: #0000FF;">this</span><span style="color: #000000;">.log </span><span style="color: #000000;">=</span><span style="color: #000000;"> log;   
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> SomeClass() : </span><span style="color: #0000FF;">this</span><span style="color: #000000;">(() </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> Log.Instance)
    { }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">int</span><span style="color: #000000;"> Calculate()
    {
        log().Information(</span><span style="color: #800000;">"</span><span style="color: #800000;">client</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Doing something...</span><span style="color: #800000;">"</span><span style="color: #000000;">);

        </span><span style="color: #008000;">//</span><span style="color: #008000;"> do calculations</span><span style="color: #008000;">
</span><span style="color: #000000;">        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> </span><span style="color: #800080;">0</span><span style="color: #000000;">;
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Notice the difference in the way that SomeClass is instantiated now.&#160; Instead of passing a log instance, I pass a function that gets the log instance.&#160; That is because caching a reference to a singleton is a bad idea – the singleton instance has the right to change out from under me.&#160; Instead, the default behavior of SomeClass will ask for the singleton instance every time, but it is no longer **required** to use the singleton implementation of ILog.&#160; Instead, I can test this in a very similar way (the difference here is subtle, but important… notice that I pass GetLoggerStub as a function, not the result of GetLoggerStub()):

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:880d88e4-3e70-40cd-856f-adc8c0bfa085" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">[TestFixture]
</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> SomeClassTests
{
    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_That_Calculate_Returns_Zero()
    {
        var test </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> SomeClass(GetLoggerStub);

        Assert.That(test.Calculate(), Is.EqualTo(</span><span style="color: #800080;">0</span><span style="color: #000000;">));
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Unfortunately, this is not always easy either.&#160; The class I am refactoring may already have a complicated set of constructors.&#160; It may be the case that these are public objects that I don’t want to change right now.&#160; There are several reasons why this might not work for me.&#160; The next option: Leave the Log class as a singleton, but don’t modify the consumer in any way.&#160; Instead, put a shim in the singleton.

### Put a Shim in it

This tends to be the technique I use most often, because it creates the least amount of friction when it comes to refactoring the classes for testability.&#160; After all, if there are no tests to validate that I don’t break anything in my re-factoring, I don’t want to make drastic changes.&#160; The consumer doesn’t need to change or know about the changes to the Log class.&#160; It kind of breaks up the singleton pattern a bit (there can be TWO instances!), but all is fair in love and testing, right?&#160; The modified:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:2ed5053d-2214-4122-be61-761ce69eaa1c" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> Log : ILog
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> Log()
    {
        </span><span style="color: #008000;">//</span><span style="color: #008000;"> Set up a way to write to a logging service somewhere</span><span style="color: #008000;">
</span><span style="color: #000000;">    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> ILog theOnlyOne;
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> ILog testOverride;

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> ILog Instance
    {
        </span><span style="color: #0000FF;">get</span><span style="color: #000000;">
        {
            </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (testOverride </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
                </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> testOverride;

            </span><span style="color: #0000FF;">if</span><span style="color: #000000;">(theOnlyOne </span><span style="color: #000000;">==</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
                theOnlyOne </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Log();

            </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> theOnlyOne;
        }
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> OverrideInstanceForTesting(ILog log)
    {
        testOverride </span><span style="color: #000000;">=</span><span style="color: #000000;"> log;
    }

    </span><span style="color: #008000;">//</span><span style="color: #008000;"> Everything else the same</span><span style="color: #008000;">
</span><span style="color: #000000;">}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

Again, I don’t have to modify my consumer class, but my test fixture has to do a bit more work:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:5d13498c-a681-4e80-93d0-0354eefb4f8f" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> SomeClassTests
{
    [SetUp]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">virtual</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> SetUp()
    {
        Log.OverrideInstanceForTesting(GetLoggerStub());
    }

    [TearDown]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">virtual</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> TearDown()
    {
        Log.OverrideInstanceForTesting(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">);
    }

    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_That_Calculate_Returns_Zero()
    {
        var test </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> SomeClass();

        Assert.That(test.Calculate(), Is.EqualTo(</span><span style="color: #800080;">0</span><span style="color: #000000;">));
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

This option might not be the available either.&#160; If the singleton is a publically consumed service and I can’t modify it for some reason (I don’t own it, for instance), then I can wrap it.

### Wrap it

This is a technique I use when Microsoft has given me a static class or singleton that I don’t want a direct dependency to, but they also didn’t give me an interface to substitute in either.&#160; I just create my own interface that looks a lot like the singleton and wrap it!

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:304d40ac-6f93-4749-803e-2a27ee49cb35" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> LogWrapper : ILog
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Error(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message)
    {
        Log.Instance.Error(category, message);
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Warning(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message)
    {
        Log.Instance.Warning(category, message);
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Information(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> category, </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> message)
    {
        Log.Instance.Information(category, message);
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

With that, I can use the same modification to the consumer as in “Re-Factor Out the Singleton”.&#160; The test also looks the same as “Re-Factor Out the Singleton”.

### In Summary

There are other ways to deal with this problem.&#160; I could use [TypeMock Islolator](http://www.typemock.com/index.php) to replace the singleton implementation with a new one.&#160; I have even seen some suggestions that you can use a [post-compiler code injection library](http://www.codethinked.com/post/2009/04/26/Static-Method-Interception-in-NET-with-C-and-MonoCecil.aspx) to replace the singleton implementation.&#160; These, to me, are cheating.&#160; They get around a problem that you have in your system: tight coupling.&#160; I much prefer the strategies mentioned in this post for dealing with singletons because they allow you to make baby steps towards loose coupling.&#160; Over time, these re-factorings add up and push you in the direction of having testable code.