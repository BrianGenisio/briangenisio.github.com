---
title: Adventures in MVVM -- Dependant Properties with INotifyPropertyChanged
date: 2009-11-14 13:54:00
layout: post
category: Software
tags: [MVVM]
permalink: /archives/2009/11/14/adventures-in-mvvm-dependant-properties-with-inotifypropertychanged/
---

[More Adventures in MVVM](/archives/2009/05/22/adventures-in-mvvm-model-view-viewmodel/) 

I spend a lot of time writing ViewModels, which almost always implements **INotifyPropertyChanged**.&#160; For those who are not familiar with this interface, it includes a single event: PropertyChanged.&#160; That event contains a payload of the name of the property that changed.&#160; It exists as a standard way to notify observers that a property needs to be re-evaluated.

Although I use it all the time, I have always believed that **INotifyPropertyChanged** has some serious shortcomings.&#160; One of those shortcomings deals with dependant properties. 

Lets say, for for the sake of example, that the ViewModel has two integer properties that the user can enter (**InputA** and **InputB**).&#160; There also exists a property named **Calculation** that is dependent upon **InputA** and **InputB**.&#160; Finally, there is a display property named **CalculationText** which is dependent upon **Calculation**.&#160; The code for these properties would look like this:

```csharp
private int _inputA;
public int InputA
{
    get { return _inputA; }
    set
    {
        if (_inputA == value) return;

        _inputA = value;
        RaisePropertyChanged("InputA");
        RaisePropertyChanged("Calculation");
        RaisePropertyChanged("CalculationText");
    }
}

private int _inputB;
public int InputB
{
    get { return _inputB; }
    set
    {
        if (_inputB == value) return;

        _inputB = value;
        RaisePropertyChanged("InputB");
        RaisePropertyChanged("Calculation");
        RaisePropertyChanged("CalculationText");
    }
}

public int Calculation
{
    get { return InputA * InputB; }
}

public string CalculationText
{
    get { return "A * B = " + Calculation; }
}
```

There is a problem with this code.&#160; The input properties need to know that they are inputs for **Calculation** and **CalculationText** by firing the event for the calculations.&#160; As far as I am concerned, this is the wrong place for this information to exist.&#160; Inputs should not know that they are inputs.&#160; I can say first hand that this quickly breaks down as the ViewModel gets bigger and you start changing behavior.

The responsibility should be reversed.&#160; Instead of having the input properties knowing about the dependants, the dependent properties should be responsible for knowing the inputs that they rely upon.&#160; This can be done with some simple declarations:

```csharp
private int _inputA;
public int InputA
{
    get { return _inputA; }
    set
    {
        if (_inputA == value) return;

        _inputA = value;
        RaisePropertyChanged("InputA");
    }
}

private int _inputB;
public int InputB
{
    get { return _inputB; }
    set
    {
        if (_inputB == value) return;

        _inputB = value;
        RaisePropertyChanged("InputB");
    }
}

[DependsUpon("InputA")]
[DependsUpon("InputB")]
public int Calculation
{
    get { return InputA * InputB; }
}

[DependsUpon("Calculation")]
public string CalculationText
{
    get { return "A * B = " + Calculation; }
}
```

Notice how **Calculation** declares that it is dependent upon **InputA** and **InputB**.&#160; **CalculationText** also declares a dependency upon **Calculation**.&#160; The inputs are free of knowing anything about the dependants.

The implementation of this behavior should be handled in the base class and there are several ways to accomplish it.&#160; There are just a few things you want to think about:

1.  Make sure that when inputs fire a change notification, the dependants are also fired2.  Make sure to handle dependency chaining.&#160; **InputA** will cause **Calculation** to fire and **Calculation** will cause **CalculationText** to fire3.  If the derived class declares dependencies such that it creates a circular reference, don’t endlessly loop or overflow your stack

Here is a very simple implementation of this behavior.&#160; It manages the reflection at the time of change.&#160; A better (more efficient) implementation would probably map the dependencies at construction time into a private dictionary, but this is just a quick example of how you might achieve this behavior:

```csharp
public abstract class ViewModelBase : INotifyPropertyChanged
{
    [AttributeUsage(AttributeTargets.Property, AllowMultiple = true)]
    protected class DependsUponAttribute : Attribute
    {
        public string DependancyName { get; private set; }

        public DependsUponAttribute(string propertyName)
        {
            DependancyName = propertyName;
        }
    }

    protected virtual void RaisePropertyChanged(string propertyName)
    {
        var handlers = PropertyChanged;
        if (handlers != null)
        {
            foreach(var property in AllNotifiedProperties(propertyName))
                handlers(this, new PropertyChangedEventArgs(property));
        }
    }

    private IEnumerable<string> DependantProperties(string inputName)
    {
        return from property in GetType().GetProperties()
               where property.GetCustomAttributes(typeof(DependsUponAttribute), true).Cast<DependsUponAttribute>()
                     .Any(attribute => attribute.DependancyName == inputName)
               select property.Name;
    }

    private IEnumerable<string> NotifiedProperties(IEnumerable<string> inputs)
    {
        var dependancies = from input in inputs
                           from dependancy in DependantProperties(input)
                           select dependancy;

        return inputs.Union(dependancies).Distinct();
    }

    private IEnumerable<string> AllNotifiedProperties(string inputName)
    {
        IEnumerable<string> results = new[]{inputName};

        while (NotifiedProperties(results).Count() > results.Count())
            results = NotifiedProperties(results);

        return results;
    }

    public event PropertyChangedEventHandler PropertyChanged;
}
```
</div>