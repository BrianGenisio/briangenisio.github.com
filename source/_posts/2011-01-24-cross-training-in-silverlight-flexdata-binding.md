---
title: 'Cross-Training in Silverlight & Flex–Data Binding'
tags:
  - Cross-Training
  - Data Binding
  - Flex
  - Silverlight
id: 264
categories:
  - Uncategorized
date: 2011-01-24 01:57:39
---

More [Cross-Training in SIlverlight and Flex](http://houseofbilz.com/archives/2010/10/24/cross-training-in-silverlight-vs-flex/)

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchives%2F2011%2F01%2F24%2Fcross-training-in-silverlight-flexdata-binding%2F)](http://dotnetshoutout.com/Cross-Training-in-Silverlight-Flex-Data-Binding)

One of the most powerful and compelling features of RIA frameworks like Silverlight and Flex is data binding.&#160; Data binding is where you declaratively state that a property in the view is “bound” to data in your model (or [presentation behavior layer](http://houseofbilz.com/archives/2010/12/29/cross-training-in-silverlight-flexmvvm-vs-presentation-model/)).&#160; This means that when the model data changes, the UI is updated.&#160; Conversely, when a user sets something in the UI layer, the model is then updated.

This capability greatly increases the ability to write features by reducing the amount of value management code the developer has to write.&#160; Data binding makes patterns like [MVVM and Presentation Model](http://houseofbilz.com/archives/2010/12/29/cross-training-in-silverlight-flexmvvm-vs-presentation-model/) possible because it eliminates boiler-plate code that is often found in other patterns like Model-View-Presenter.&#160; Data binding, in my opinion, is one of the features of modern application stacks that sets them apart from their ancestors.

In this article, I will discuss the different ways you might want to use data binding in Silverlight and Flex.&#160; I will describe how the approaches differ.&#160; You will see how similar they are as well.&#160; All of these examples can be found on my BitBucket site where all of my [“Cross-Training” examples](https://bitbucket.org/briangenisio/crosstraining/src) can be found.
  <div style="background-color: #e8f3ff">   

### <font style="font-weight: bold">Data Binding in Silverlight</font>

Data binding in Silverlight is purely declarative (as opposed to Flex, which is expression-based).&#160; For any given property in the visual tree, the developer can use a binding syntax to declaratively state how to bind.&#160; Silverlight conventions have you set a “data context” for a visual element in which the binding declaration works against.&#160; These bindings can be created imperatively in code, but they are typically defined in XAML unless there is a more dynamic reason to do it differently.

#### <font style="font-weight: bold">Data Contexts</font>

You define data contexts for a visual tree.&#160; When the data context is set, it remains in scope for all descendants in the visual tree unless is is set again.&#160; Data contexts can be set in XAML or in code and they can be any object.&#160; Here is an example of setting a data context that will be used for all of the examples to come:
    <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;UserControl.Resources&gt;
    &lt;local:DataModel x:Name=&quot;dataModel&quot; SimpleText=&quot;default&quot;&gt;
        &lt;local:DataModel.Person&gt;
            &lt;local:Person&gt;
                &lt;local:Person.FullName&gt;
                    &lt;local:PersonName First=&quot;Brian&quot; Last=&quot;Genisio&quot; /&gt;
                &lt;/local:Person.FullName&gt;
            &lt;/local:Person&gt;
        &lt;/local:DataModel.Person&gt;
    &lt;/local:DataModel&gt;
&lt;/UserControl.Resources&gt;

&lt;StackPanel DataContext=&quot;{StaticResource dataModel}&quot;&gt;
&lt;/StackPanel&gt;</pre>

All of the examples in this post will be inside the **StackPanel** so they will inherit the data context from their visual parent.&#160; There are certainly other ways of setting the **DataContext** but this is how we will do it for this post.

#### <font style="font-weight: bold">Basic Example</font>

Now that we have seen an example of setting the data context, lets bind to the **SimpleText** property in the data model:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;TextBlock Text=&quot;{Binding SimpleText}&quot; /&gt;</pre>

By using this simple binding syntax, the text block in the UI will show the value of **SimpleText**.&#160; In this case, the UI will show “default”.

#### <font style="font-weight: bold">Change Notification</font>

Displaying data is great and very often, this is all you need.&#160; Often, though, you need to update the UI when the property changes.&#160; It becomes the data object’s responsibility to notify the listener (the UI in this case) of the change so it knows to modify the text.

Typically, this is done with the **INotifyPropertyChanged** interface.&#160; The interface defines one event – **PropertyChanged** – which sends the name of the property that changed.&#160; The interface is not specific to the UI layer but the UI layer knows how to process the event through the binding you created in the XAML.&#160; 

This is how you implement **INotifyPropertyChanged **in the **DataModel** class:

  <pre class="brush: csharp; ruler: true; gutter: false; toolbar: false;">public class DataModel : INotifyPropertyChanged
{
    private string _simpleText = &quot;first&quot;;
    public string SimpleText
    {
        get { return _simpleText; }
        set
        {
            if (_simpleText == value)
                return;

            _simpleText = value;
            OnPropertyChanged(&quot;SimpleText&quot;);
        }
    }

    protected void OnPropertyChanged(string property)
    {
        var handlers = PropertyChanged;
        if (handlers != null)
            handlers(this, new PropertyChangedEventArgs(property));
    }

    public event PropertyChangedEventHandler PropertyChanged;
}</pre>

The first part of this class defines the notifiable property using a common pattern.&#160; It uses a private backing store (**_simpleText**) and delegates to it in the property getter and setter.&#160; The setter checks to make sure the value has changed so it doesn’t send an event when nothing changed.&#160; It then sets the backing store and fires an event saying “SimpleText” has changed.

The second part of this code implements the event that was defined in the **INotifyPropertyChanged** interface.&#160; It uses a very common event pattern of using a protected **OnPropertyChanged** method.&#160; Inside, it makes a copy of the handlers to avoid any changes that may happen while looping through the handlers.&#160; It then calls the handlers if they exist.&#160; It is very common to push the second part of this class into a common **Notifiable** class.

Now that the **DataModel** can notify the UI of changes to **SimpleText**, you can can set the property being changed:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;StackPanel Orientation=&quot;Horizontal&quot;&gt;
    &lt;TextBlock Text=&quot;{Binding SimpleText}&quot; /&gt;
    &lt;Button Content=&quot;update text&quot; Click=&quot;UpdateSimpleText&quot; /&gt;
&lt;/StackPanel&gt;</pre>

  <pre class="brush: csharp; ruler: true; gutter: false; toolbar: false;">private void UpdateSimpleText(object sender, RoutedEventArgs e)
{
    (Resources[&quot;dataModel&quot;] as DataModel).SimpleText = &quot;updated&quot;;
}</pre>

In this example, the value begins with “default” just as the before.&#160; When you press the “update text” button, the **SimpleText** property is modified.&#160; Internally, the property notifies the UI of the change and user sees “updated”.

#### <font style="font-weight: bold">Dependency Properties</font>

Visual elements within Silverlight use a different mechanism for property notification.&#160; Dependency Properties are the preferred method for creating bindable properties on UI elements.&#160; More specifically, any class that derives from **DependencyObject** should avoid using **INotifyPropertyChanged** and use Dependency Properties instead.

Dependency Properties are created imperatively in the code behind of the visual element.&#160; Add a Dependency Property to the **MainPage** user control:

  <pre class="brush: csharp; ruler: true; gutter: false; toolbar: false;">public static DependencyProperty LocalTextProperty = 
    DependencyProperty.Register(&quot;LocalText&quot;, 
        typeof(String), typeof(MainPage), 
        new PropertyMetadata(&quot;&quot;));

public string LocalText
{
    get { return GetValue(LocalTextProperty) as string; }
    set { SetValue(LocalTextProperty, value); }
}</pre>

With this property created, you can now bind to a property in my user control, instead of a data object:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;StackPanel Orientation=&quot;Horizontal&quot;&gt;
    &lt;TextBox Text=&quot;{Binding ElementName=root, Path=LocalText,  Mode=TwoWay}&quot; Width=&quot;100&quot; /&gt;
    &lt;TextBox Text=&quot;{Binding ElementName=root, Path=LocalText}&quot; Width=&quot;100&quot; /&gt;
&lt;/StackPanel&gt;</pre>

There are a few things to notice here:

*   **ElementName** refers to the root control, which is of type **MainPage**.&#160; This ignores the inherited data context.*   In the first **TextBox**, the **Mode** is set to **TwoWay**.&#160; This tells the control to push any changes back to the property.&#160; Since the property has notification built in (since it is a Dependency Property), the second control will get updated automatically; it is bound to the same property.

As a rule of thumb, use Dependency Properties if your class derives from **DependencyObject**.&#160; Use **INotifyPropertyChanged** in all other cases.

#### <font style="font-weight: bold">Binding to Other Elements</font>

In addition to binding to data, you may also need to bind to values in the view.&#160; The previous example shows some of this; the data contexts for the text boxes are the root elements instead of whatever was set earlier.

You can bind to another element by name:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;StackPanel Orientation=&quot;Horizontal&quot;&gt;
    &lt;TextBox Width=&quot;100&quot; x:Name=&quot;input&quot; /&gt;
    &lt;TextBox Width=&quot;100&quot; Text=&quot;{Binding ElementName=input, Path=Text}&quot; /&gt;
&lt;/StackPanel&gt;</pre>

In this example, the text of the second **TextBox** is bound to the text of the first **TextBox**.&#160; Since this binding is not **TwoWay**, the value changes are not reciprocal.

#### <font style="font-weight: bold">Binding to Chained Properties</font>

In all the examples thus far, data binding has happened on a property of the data context.&#160; This may not be the case when the data’s object tree is more complex.&#160; You can get the **First** and **Last** name properties, which are properties of a different object, from the data model using property chaining:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;TextBlock Text=&quot;{Binding Person.FullName.First'}&quot; /&gt;</pre>

The same notification rules apply; the UI will update if **First** is a notifiable property.

#### <font style="font-weight: bold">Formatted Text</font>

If you need to format the text display of a given value, you can do so using the **StringFormat** attribute in the binding declaration.&#160; Building on the previous example, the text is displayed as “Your name is: Brian”:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;TextBlock Text=&quot;{Binding Person.FullName.First, StringFormat='Your name is: {0}'}&quot; /&gt;</pre>

#### <font style="font-weight: bold">Value Converters</font>

Often, the format of the data is more complex than a simple string format.&#160; In cases like this, you can use Value Converters.&#160; These are classes that implement **IValueConverter** and provide functions for **Convert** and **ConvertBack.**&#160; Whenever binding occurs, the value is passed to the Value Converter which in turn translates the data to be displayed in the UI.&#160; 

Here is an example displaying a number with two digits of precision:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;UserControl.Resources&gt;
    &lt;local:NumberValueConverter x:Name=&quot;numeric&quot; Precision=&quot;2&quot; /&gt;
&lt;/UserControl.Resources&gt;

&lt;TextBox Width=&quot;100&quot; x:Name=&quot;number&quot; /&gt;
&lt;TextBlock Text=&quot;{Binding ElementName=number, Path=Text, Converter={StaticResource numeric}}&quot; /&gt;</pre>

The **NumberValueConverter** handles the conversion from a string value to a double and back to a string with the specified level of precision.&#160; There are not many built-in Value Converters, but they are easy to build:

  <pre class="brush: csharp; ruler: true; gutter: false; toolbar: false;">public class NumberValueConverter : IValueConverter
{
    public NumberValueConverter()
    {
        Precision = 4;
    }

    public int Precision { get; set; }

    public object Convert(object value, System.Type targetType, object parameter, System.Globalization.CultureInfo culture)
    {
        if (!(value is string))
            return DependencyProperty.UnsetValue;

        if (string.IsNullOrWhiteSpace(value as string))
            value = &quot;0&quot;;

        if ((value as string).EndsWith(&quot;.&quot;))
            value = value + &quot;0&quot;;

        var format = string.Format(&quot;{{0:F{0}}}&quot;, Precision);

        return string.Format(format, double.Parse(value as string));
    }

    public object ConvertBack(object value, System.Type targetType, object parameter, System.Globalization.CultureInfo culture)
    {
        throw new System.NotImplementedException();
    }
}</pre>

#### <font style="font-weight: bold">Summary</font>

Data binding is very powerful in Silverlight.&#160; The previous examples highlighted the most common case data binding scenarios.&#160; Certainly there are more details to cover for more complex cases, but you should understand data binding capabilities pretty well by now.&#160; 

Next, let’s see how Flex implements the same features.

</div>

<div style="background-color: #ffe8e8">

### <font style="font-weight: bold"></font>

### <font style="font-weight: bold">Data Binding in Flex</font>

Data binding in Flex is expression-based (as opposed to Silveright, which is declarative).&#160; For any given property in the visual tree, the developer can use a binding syntax to declaratively state how to bind.&#160; Since Flex is expression-based, it does not require a data context like in Silverlight.&#160; These bindings can be created imperatively in code, but they are typically defined in MXML unless there is a more dynamic reason to do it differently.

#### <font style="font-weight: bold">Basic Example</font>

To begin with, let us assume that we have a data object defined some place:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;fx:Declarations&gt;
    &lt;local:DataObject id=&quot;dataObject&quot; simpleText=&quot;default&quot; /&gt;
&lt;/fx:Dexlarations&gt;</pre>

#### <font style="font-weight: bold"></font>

Since we named the data object with the id of **dataObject**, we can use it in a binding expression anywhere within scope:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;s:Label text=&quot;{dataObject.simpleText}&quot; /&gt;</pre>

When the MXML code is compiled, the binding expression is compiled as well.&#160; It gets turned into ActionScript 3 code just like everything else in the MXML markup.&#160; The compiler re-writes the class to set the text from the **dataObject** object as defined in the expression.

#### <font style="font-weight: bold">Change Notification</font>

Displaying data is great and very often, this is all you need.&#160; Often, though, you need to update the UI when the property changes.&#160; It becomes the data object’s responsibility to notify the listener (the UI) of the change so it knows to modify the text.

Typically, this is done with Bindable Properties.&#160; This is how you do it in the **DataObject** class:

  <pre class="brush: as3; ruler: true; gutter: false; toolbar: false;">[Bindable]
public class DataObject
{
    public var simpleText:String;
}</pre>

Notice the [**Bindable]** meta tag that has been added to the **DataObject** class.&#160; This meta tag tells the Flex compiler to re-write the public properties in this class to be notifiable.&#160; It does this by employing the standard eventing mechanism found in ActionScript 3.&#160; 

In reality, the generated code looks a lot like the Silverlight notifiable properties; the key difference is that the **Bindable** flag does it for you.&#160; There is no specific notifiable interface for notifiable properties but the code-generated convention is to name the event as “propertyChanged” where “property” is the actual name of the property; in this example, it is “simpleTextChanged”.

Enough of the details.&#160; By doing this, you can have code like this that will update the text based on data binding when you press the button:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;s:Label text=&quot;{dataObject.simpleText}&quot; /&gt;
&lt;s:Button label=&quot;update&quot; click=&quot;dataObject.simpleText = 'Updated'&quot; /&gt;</pre>

The Flex compiler parses the binding expression and listens to change events on the **dataObject** instance.&#160; When it changes, the **Label.text** property is set.&#160; All of this is generated for you.

#### <font style="font-weight: bold">Binding to Local Variables</font>

It is easy to bind to local variables since reading a local variable is noting but an expression in Flex.&#160; For instance, you can define the variable as **[Bindable] **in the script block:

  <pre class="brush: as3; ruler: true; gutter: false; toolbar: false;">[Bindable]
private var localText:String = &quot;&quot;;</pre>

Then you simply bind to the variable:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;s:TextInput text=&quot;@{localText}&quot; /&gt;
&lt;s:TextInput text=&quot;{localText}&quot; /&gt;</pre>

Notice that the first text box use the “@” symbol.&#160; This tells the binding system that the binding is two-way.&#160; In other words, anything typed into the first **TextInput** will get set in the **localText** variable and then sent back to the second **TextInput**.

#### <font style="font-weight: bold">Using Expressions</font>

<font style="font-weight: bold"></font>

The power of using expressions for data binding becomes obvious when you call methods within the binding.&#160; In this example, we can add two values using data binding alone.&#160; When **a** or **b** change, the binding expression is automatically re-evaluated, causing the output to change:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;s:TextInput id=&quot;a&quot; /&gt;
&lt;s:TextInput id=&quot;b&quot; /&gt;
&lt;s:Label text=&quot;a + b = {add(Number(a.text), Number(b.text))}&quot; /&gt;</pre>

Although I don’t recommend putting too much logic in the binding expressions, the power provided here is quite amazing.&#160; One way I find myself using this feature while implementing the Presentation Model pattern is by hiding elements based on if data exists.&#160; A simple example of binding the **visible** property to the existence of the **nullableObject** variable:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;s:Label text=&quot;Value is not null&quot; visible=&quot;{nullableObject != null}&quot; /&gt;
&lt;s:Button label=&quot;Make null&quot; click=&quot;nullableObject = null&quot; /&gt;
&lt;s:Button label=&quot;Set value&quot; click=&quot;nullableObject = 'something'&quot; /&gt;</pre>

#### <font style="font-weight: bold">Binding to Other Elements</font>

In addition to binding to data, you may also need to bind to values in the view.&#160; You can bind to another element by **id** since setting an **id** on a visual element automatically references it in a variable of the same name:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;s:TextInput id=&quot;input&quot; /&gt;
&lt;s:TextInput text=&quot;{input.text}&quot; /&gt;</pre>

In this example, the text of the second **TextInput **is bound to the text of the first **TextInput**.&#160; Since this binding is not **TwoWay**, the value changes are not reciprocal.

#### <font style="font-weight: bold">Binding to Chained Properties</font>

In all the examples thus far, data binding has happened on a property of a single object.&#160; This may not be the case when the data’s object tree is more complex.&#160; You can get the **First** and **Last** name properties out of the data model by using property chaining:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;fx:Declarations&gt;
    &lt;fx:Model id=&quot;model&quot;&gt;
        &lt;person&gt;
            &lt;name&gt;
                &lt;first&gt;Brian&lt;/first&gt;
                &lt;last&gt;Genisio&lt;/last&gt;
            &lt;/name&gt;
        &lt;/person&gt;
    &lt;/fx:Model&gt;
&lt;/fx:Declarations&gt;

&lt;s:Label text=&quot;{model.name.first}&quot; /&gt;</pre>

The same notification rules apply; meaning that the UI will update if **First** is a notifiable property.&#160; In the case of **fx:Model**, all properties are inherently bindable.

In order for change notification to occur on the chained properties to update on change, all of the properties in the chain must be **[Bindable].**

#### <font style="font-weight: bold">Formatted Text</font>

Since data binding is based on an evaluated expression, adding text before or after the bound value is very easy:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;s:Label text=&quot;Your name is: {model.name.first}&quot; /&gt;</pre>

<font style="font-weight: bold"></font>

In this example, the UI will display “Your name is: Brian”.

#### <font style="font-weight: bold">Formatters</font>

Since data binding is based on expressions, data can be formatted using functions in the expression that get evaluated every time a bindable property changes.&#160; In addition, Flex provides several formatters out of the box.&#160; The following example shows how to set the precision of a double value to 2 digits after the decimal place:

  <pre class="brush: xml; ruler: true; gutter: false; toolbar: false;">&lt;fx:Declarations&gt;
    &lt;mx:NumberFormatter id=&quot;numeric&quot; precision=&quot;2&quot; /&gt;
&lt;/fx:Declarations&gt;

&lt;s:TextInput id=&quot;number&quot; /&gt;
&lt;s:Label text=&quot;{numeric.format(Number(number.text))}&quot; /&gt;</pre>

Whenever the **number.text** property changes, it uses the **NumberFormatter** to evaluate the text in the **Label.&#160; **In addition to the numerous built-in formatters (currency, date, phone number, etc), you can create your own formatters by extending **Formatter**.&#160; 

</div>

### <font style="font-weight: bold">Summary</font>

The capabilities provided by data binding in Silverlight and Flex give the developer an extremely powerful tool.&#160; It allows him/her to write more features with less code.&#160; Silverlight uses a declarative model where Flex uses an expression model, but the majority of the capabilities mirror each other.&#160; Data binding is a case where I think Flex has the upper hand, though.&#160; By using expressions, the data binding is both more succinct and more powerful.&#160; That is not to say that the declarative style in Silverlight is bad.&#160; It is just that the expressive side in Flex is better (in my opinion).&#160; 

I am VERY curious to see how C# 5 with the compiler as a service adds value to the binding story in Silverlight.&#160; Will we see an optional expression-based binding that is compiled and evaluated at runtime?&#160; If not, how hard will it be to write this?

Next time, I will write about visualizing data in Silverlight and Flex. Stay tuned ![Smile](http://houseofbilz.com/wp-content/uploads/2011/01/wlEmoticon-smile.png)