---
title: Adventures in MVVM -- Dependant Properties with INotifyPropertyChanged
id: 80
categories:
  - Uncategorized
date: 2009-11-14 13:54:00
tags:
---

[More Adventures in MVVM](http://houseofbilz.com/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchive%2F2009%2F11%2F14%2Fadventures-in-mvvm----dependant-properties-with-inotifypropertychanged.aspx)](http://dotnetshoutout.com/Adventures-in-MVVM-Dependant-Properties-with-INotifyPropertyChanged) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f11%2f14%2fadventures-in-mvvm----dependant-properties-with-inotifypropertychanged.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2009%2f11%2f14%2fadventures-in-mvvm----dependant-properties-with-inotifypropertychanged.aspx)

I spend a lot of time writing ViewModels, which almost always implements **INotifyPropertyChanged**.&#160; For those who are not familiar with this interface, it includes a single event: PropertyChanged.&#160; That event contains a payload of the name of the property that changed.&#160; It exists as a standard way to notify observers that a property needs to be re-evaluated.

Although I use it all the time, I have always believed that **INotifyPropertyChanged** has some serious shortcomings.&#160; One of those shortcomings deals with dependant properties. 

Lets say, for for the sake of example, that the ViewModel has two integer properties that the user can enter (**InputA** and **InputB**).&#160; There also exists a property named **Calculation** that is dependent upon **InputA** and **InputB**.&#160; Finally, there is a display property named **CalculationText** which is dependent upon **Calculation**.&#160; The code for these properties would look like this:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:d20f4eef-cddf-457c-be1b-5cd0419e6024" class="wlWriterSmartContent">   <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> _inputA;
</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> InputA
{
    </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> _inputA; }
    </span><span style="color: #0000ff">set</span><span style="color: #000000">
    {
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (_inputA </span><span style="color: #000000">==</span><span style="color: #000000"> value) </span><span style="color: #0000ff">return</span><span style="color: #000000">;

        _inputA </span><span style="color: #000000">=</span><span style="color: #000000"> value;
        RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">InputA</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
        RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Calculation</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
        RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">CalculationText</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
    }
}

</span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> _inputB;
</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> InputB
{
    </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> _inputB; }
    </span><span style="color: #0000ff">set</span><span style="color: #000000">
    {
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (_inputB </span><span style="color: #000000">==</span><span style="color: #000000"> value) </span><span style="color: #0000ff">return</span><span style="color: #000000">;

        _inputB </span><span style="color: #000000">=</span><span style="color: #000000"> value;
        RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">InputB</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
        RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Calculation</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
        RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">CalculationText</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
    }
}

</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> Calculation
{
    </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> InputA </span><span style="color: #000000">*</span><span style="color: #000000"> InputB; }
}

</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> CalculationText
{
    </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> </span><span style="color: #800000">&quot;</span><span style="color: #800000">A * B = </span><span style="color: #800000">&quot;</span><span style="color: #000000"> </span><span style="color: #000000">+</span><span style="color: #000000"> Calculation; }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

There is a problem with this code.&#160; The input properties need to know that they are inputs for **Calculation** and **CalculationText** by firing the event for the calculations.&#160; As far as I am concerned, this is the wrong place for this information to exist.&#160; Inputs should not know that they are inputs.&#160; I can say first hand that this quickly breaks down as the ViewModel gets bigger and you start changing behavior.

The responsibility should be reversed.&#160; Instead of having the input properties knowing about the dependants, the dependent properties should be responsible for knowing the inputs that they rely upon.&#160; This can be done with some simple declarations:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:f2850de4-e87b-4d5b-88cb-a83a57fbb89e" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> _inputA;
</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> InputA
{
    </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> _inputA; }
    </span><span style="color: #0000ff">set</span><span style="color: #000000">
    {
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (_inputA </span><span style="color: #000000">==</span><span style="color: #000000"> value) </span><span style="color: #0000ff">return</span><span style="color: #000000">;

        _inputA </span><span style="color: #000000">=</span><span style="color: #000000"> value;
        RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">InputA</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
    }
}

</span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> _inputB;
</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> InputB
{
    </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> _inputB; }
    </span><span style="color: #0000ff">set</span><span style="color: #000000">
    {
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (_inputB </span><span style="color: #000000">==</span><span style="color: #000000"> value) </span><span style="color: #0000ff">return</span><span style="color: #000000">;

        _inputB </span><span style="color: #000000">=</span><span style="color: #000000"> value;
        RaisePropertyChanged(</span><span style="color: #800000">&quot;</span><span style="color: #800000">InputB</span><span style="color: #800000">&quot;</span><span style="color: #000000">);
    }
}

[DependsUpon(</span><span style="color: #800000">&quot;</span><span style="color: #800000">InputA</span><span style="color: #800000">&quot;</span><span style="color: #000000">)]
[DependsUpon(</span><span style="color: #800000">&quot;</span><span style="color: #800000">InputB</span><span style="color: #800000">&quot;</span><span style="color: #000000">)]
</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">int</span><span style="color: #000000"> Calculation
{
    </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> InputA </span><span style="color: #000000">*</span><span style="color: #000000"> InputB; }
}

[DependsUpon(</span><span style="color: #800000">&quot;</span><span style="color: #800000">Calculation</span><span style="color: #800000">&quot;</span><span style="color: #000000">)]
</span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> CalculationText
{
    </span><span style="color: #0000ff">get</span><span style="color: #000000"> { </span><span style="color: #0000ff">return</span><span style="color: #000000"> </span><span style="color: #800000">&quot;</span><span style="color: #800000">A * B = </span><span style="color: #800000">&quot;</span><span style="color: #000000"> </span><span style="color: #000000">+</span><span style="color: #000000"> Calculation; }
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>

Notice how **Calculation** declares that it is dependent upon **InputA** and **InputB**.&#160; **CalculationText** also declares a dependency upon **Calculation**.&#160; The inputs are free of knowing anything about the dependants.

The implementation of this behavior should be handled in the base class and there are several ways to accomplish it.&#160; There are just a few things you want to think about:

1.  Make sure that when inputs fire a change notification, the dependants are also fired2.  Make sure to handle dependency chaining.&#160; **InputA** will cause **Calculation** to fire and **Calculation** will cause **CalculationText** to fire3.  If the derived class declares dependencies such that it creates a circular reference, don’t endlessly loop or overflow your stack

Here is a very simple implementation of this behavior.&#160; It manages the reflection at the time of change.&#160; A better (more efficient) implementation would probably map the dependencies at construction time into a private dictionary, but this is just a quick example of how you might achieve this behavior:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:525d9dea-2b00-453f-bcef-2a764c529c32" class="wlWriterSmartContent">
  <pre style="background-color: #e4e4e4; overflow: auto"><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">abstract</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> ViewModelBase : INotifyPropertyChanged
{
    [AttributeUsage(AttributeTargets.Property, AllowMultiple </span><span style="color: #000000">=</span><span style="color: #000000"> </span><span style="color: #0000ff">true</span><span style="color: #000000">)]
    </span><span style="color: #0000ff">protected</span><span style="color: #000000"> </span><span style="color: #0000ff">class</span><span style="color: #000000"> DependsUponAttribute : Attribute
    {
        </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">string</span><span style="color: #000000"> DependancyName { </span><span style="color: #0000ff">get</span><span style="color: #000000">; </span><span style="color: #0000ff">private</span><span style="color: #000000"> </span><span style="color: #0000ff">set</span><span style="color: #000000">; }

        </span><span style="color: #0000ff">public</span><span style="color: #000000"> DependsUponAttribute(</span><span style="color: #0000ff">string</span><span style="color: #000000"> propertyName)
        {
            DependancyName </span><span style="color: #000000">=</span><span style="color: #000000"> propertyName;
        }
    }

    </span><span style="color: #0000ff">protected</span><span style="color: #000000"> </span><span style="color: #0000ff">virtual</span><span style="color: #000000"> </span><span style="color: #0000ff">void</span><span style="color: #000000"> RaisePropertyChanged(</span><span style="color: #0000ff">string</span><span style="color: #000000"> propertyName)
    {
        var handlers </span><span style="color: #000000">=</span><span style="color: #000000"> PropertyChanged;
        </span><span style="color: #0000ff">if</span><span style="color: #000000"> (handlers </span><span style="color: #000000">!=</span><span style="color: #000000"> </span><span style="color: #0000ff">null</span><span style="color: #000000">)
        {
            </span><span style="color: #0000ff">foreach</span><span style="color: #000000">(var property </span><span style="color: #0000ff">in</span><span style="color: #000000"> AllNotifiedProperties(propertyName))
                handlers(</span><span style="color: #0000ff">this</span><span style="color: #000000">, </span><span style="color: #0000ff">new</span><span style="color: #000000"> PropertyChangedEventArgs(property));
        }
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> IEnumerable</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">string</span><span style="color: #000000">&gt;</span><span style="color: #000000"> DependantProperties(</span><span style="color: #0000ff">string</span><span style="color: #000000"> inputName)
    {
        </span><span style="color: #0000ff">return</span><span style="color: #000000"> from property </span><span style="color: #0000ff">in</span><span style="color: #000000"> GetType().GetProperties()
               </span><span style="color: #0000ff">where</span><span style="color: #000000"> property.GetCustomAttributes(</span><span style="color: #0000ff">typeof</span><span style="color: #000000">(DependsUponAttribute), </span><span style="color: #0000ff">true</span><span style="color: #000000">).Cast</span><span style="color: #000000">&lt;</span><span style="color: #000000">DependsUponAttribute</span><span style="color: #000000">&gt;</span><span style="color: #000000">()
                     .Any(attribute </span><span style="color: #000000">=&gt;</span><span style="color: #000000"> attribute.DependancyName </span><span style="color: #000000">==</span><span style="color: #000000"> inputName)
               select property.Name;
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> IEnumerable</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">string</span><span style="color: #000000">&gt;</span><span style="color: #000000"> NotifiedProperties(IEnumerable</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">string</span><span style="color: #000000">&gt;</span><span style="color: #000000"> inputs)
    {
        var dependancies </span><span style="color: #000000">=</span><span style="color: #000000"> from input </span><span style="color: #0000ff">in</span><span style="color: #000000"> inputs
                           from dependancy </span><span style="color: #0000ff">in</span><span style="color: #000000"> DependantProperties(input)
                           select dependancy;

        </span><span style="color: #0000ff">return</span><span style="color: #000000"> inputs.Union(dependancies).Distinct();
    }

    </span><span style="color: #0000ff">private</span><span style="color: #000000"> IEnumerable</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">string</span><span style="color: #000000">&gt;</span><span style="color: #000000"> AllNotifiedProperties(</span><span style="color: #0000ff">string</span><span style="color: #000000"> inputName)
    {
        IEnumerable</span><span style="color: #000000">&lt;</span><span style="color: #0000ff">string</span><span style="color: #000000">&gt;</span><span style="color: #000000"> results </span><span style="color: #000000">=</span><span style="color: #000000"> </span><span style="color: #0000ff">new</span><span style="color: #000000">[]{inputName};

        </span><span style="color: #0000ff">while</span><span style="color: #000000"> (NotifiedProperties(results).Count() </span><span style="color: #000000">&gt;</span><span style="color: #000000"> results.Count())
            results </span><span style="color: #000000">=</span><span style="color: #000000"> NotifiedProperties(results);

        </span><span style="color: #0000ff">return</span><span style="color: #000000"> results;
    }

    </span><span style="color: #0000ff">public</span><span style="color: #000000"> </span><span style="color: #0000ff">event</span><span style="color: #000000"> PropertyChangedEventHandler PropertyChanged;
}</span></pre>

<!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com -->

</div>