---
title: Cross-Training in Silverlight and Flex – XAML vs MXML
date: 2010-11-08 04:02:47
layout: post
category: Software
tags: [MXML, XAML, Cross-Training, Flex, RIA, Silverlight]
permalink: /archives/2010/11/08/cross-training-in-silverlight-and-flex-xaml-vs-mxml/
---

More [Cross-Training in Silverlight and Flex](/archives/2010/10/24/cross-training-in-silverlight-vs-flex/)

Silverlight and Flex both include a declarative markup language for their UIs.&#160; Microsoft calls the Silverlight specification Extensible Application Markup Language (XAML pronounced zammel).&#160; Adobe calls the Flex specification MXML which is thought to have come from the original inventors: Macromedia extensible Markup Language.

In either case, these markup languages share two strong characteristics: They are both based on XML and they both do a fine job of declaring the UI.
 
### XAML

You use XAML in Silverlight to define the user interface in a declarative way.&#160; XAML is nothing more than XML with some Silverilght namespaces that let you represent a visual tree.&#160; Following is an example of **MainPage.xaml**.
```xml
<UserControl x:Class="Hello_World.MainPage"
    xmlns="http://schemas.microsoft.com/winfx/2006/xaml/presentation"
    xmlns:x="http://schemas.microsoft.com/winfx/2006/xaml"
             Width="300" Height="36">

    <Border BorderThickness="5" CornerRadius="5"             BorderBrush="Black" Height="Auto" Width="Auto">
        <StackPanel Orientation="Horizontal">
            <TextBox Width="100" Name="Input" Margin="3" />
            <Button Content="Say Hello" Click="SayHello" Margin="3" />
            <TextBlock Name="Output" Margin="3" VerticalAlignment="Bottom" />
        </StackPanel>
    </Border>

</UserControl>
```
 
The MainPage UserControl uses this XAML code to define the entire view.&#160; A Border houses three UI components stacked together: text input, a button to do something with and a label to show the output.&#160; If you were to type this into your MainPage control in Silverlight, it would look like this in your browser:


Of course, the declarative XAML is nothing without some imperative behavior. Visual Studio, generates a corresponding “code-behind” whenever a UserControl is created. In the simplest of cases, you place your behavior in the “code-behind” file. In this case, the file is named **MainPage.xaml.cs**.&#160; By default, it creates a constructor for you that calls **InitializeComponent()**.&#160; I added the **SayHello** method that will get called whenever the button (defined in the XAML) is pressed.

```csharp
public partial class MainPage
{
    public MainPage()
    {
        InitializeComponent();
    }

    private void SayHello(object sender, RoutedEventArgs e)
    {
        Output.Text = "Hello " + Input.Text;
    }
}
```

Most XAML introductions get off the train at this point. This series aims to show the differences between Silverlight and Flex so I will continue on to describe how the XAML and the C# code get glued together.

Notice in the “code behind” that the class it generated is a **partial** class.&#160; This means that there is more to this class somewhere else.&#160; Digging deeper into the **obj/Debug** folder, you will find an intermediate file named **MainPage.g.cs**.&#160; It holds the rest of this **partial** class:


```csharp
public partial class MainPage : UserControl {

    internal System.Windows.Controls.TextBox Input;        
    internal System.Windows.Controls.TextBlock Output;        
    private bool _contentLoaded;

    [System.Diagnostics.DebuggerNonUserCodeAttribute()]
    public void InitializeComponent() {
        if (_contentLoaded) {
            return;
        }
        _contentLoaded = true;
        System.Windows.Application.LoadComponent(this, 
            new System.Uri("/Hello%20World;component/MainPage.xaml", 
                           System.UriKind.Relative));
        this.Input = ((System.Windows.Controls.TextBox)(this.FindName("Input")));
        this.Output = ((System.Windows.Controls.TextBlock)(this.FindName("Output")));
    }
}
```

We can see where some of the important “glue” is defined. The code derives from **UserControl** and defines the **InitializeComponent** method that is called in the constructor.&#160; It also generates the two named controls: **Input** and** Outut **based on the names defined in the XAML.&#160; The **InitializeComponent** method then desterilizes the XAML into an object tree.&#160; It finishes by setting the **Input** and **Output** controls to be items found in the visual tree.&#160; This is how the “code-behind” can access these controls in the **SayHello** method.

The last bit of glue happens in the compiler.&#160; The XAML file is compiled into the DLL as an “embedded resource”.&#160; This happens automatically so you don’t need to think about it.&#160; If you are curious, you can crack open the DLL with [Reflecor](http://www.red-gate.com/products/reflector/index.htm) and find the XAML packed into the DLL.&#160; The **InitializeComponent** method loads the tree from the same XAML file which is embedded at compile-time.

### MXML

MXML in Flex is used to define the user interface in a declarative way.&#160; MXML is nothing more than XML with some Flex namespaces that let you represent a visual tree.&#160; Following is an example of **Main.mxml**.

```xml
<?xml version="1.0" encoding="utf-8"?>

<s:Application xmlns:fx="http://ns.adobe.com/mxml/2009"
              xmlns:s="library://ns.adobe.com/flex/spark"
              width="300" height="40">

   <fx:Script>
       <![CDATA[
            public function sayHello():void{

                output.text = "Hello "+ input.text;

            }

        ]]>
   </fx:Script>

   <s:BorderContainer borderWeight="5" cornerRadius="5"
                      borderColor="black" minHeight="0" width="100%">
        <s:HGroup left="3" right="3" top="3" bottom="3">
            <s:TextInput width="100" id="input"/>
            <s:Button label="Say Hello" click="sayHello()" left="6"/>
            <s:Label id="output" left="6" verticalAlign="bottom" height="100%" />
        </s:HGroup>
    </s:BorderContainer>

</s:Application>
```

<span style="color: blue"><font color="#000000">This MXML code defines the entire application.&#160; It is composed of a **BorderContainer** that houses three UI components grouped together: text input, a button to do something with and a label to show the output.&#160; If you were to type this into your **Main.mxml** view in Flex, it would look like this in your browser:</font></span>

Of course, the declarative MXML is nothing without some imperative behavior.&#160; The default way to do this in Flex is to use the HTML model of **Script** tags.&#160; The **sayHello** method is defined directly in the MXML.&#160; There are better ways (in my opinion) to separate the imperative behavior from the view, but this is a simple example.

Most MXML introductions get off the train at this point.&#160; This series aims to show the differences between Silverlight and Flex, so I will continue to describe what happens to this MXML at compile-time.

Unlike Silverlight where the XAML is embedded in the compiled project and parsed at runtime, Flex takes a different approach.&#160; MXML views get parsed at compile-time and translated directly into ActionScript. The actual code that the compiler generates is transient – it gets removed after the compile step -- unless you add the –**keep-generated-actionscript** flag to the compiler.

When this happens, quite a lot of code gets generated.&#160; I don’t want to put everything on this post, but here is a snippet from the generated code:

```actionscript
public class Main extends spark.components.Application   
{
    [Bindable] public var input : spark.components.TextInput;   
    [Bindable] public var output : spark.components.Label;

    public function Main()
    {
        super();

        this.width = 300;
        this.height = 40;
        this.mxmlContentFactory = new mx.core.DeferredInstanceFromFunction(_Main_Array1_c);
    }
}
```

The constructor sets up a **_Main_Array1_c** function which calls several other methods hierarchically which creates the controls that were defined in the MXML.

The details of the generated code are not terribly important.&#160; The important thing to understand is that the MXML gets compiled into ActionScript and it is not parsed at run-time.&#160; XAML in Silverlight gets parsed at run-time but VisualStudio does generate some C# code to help glue the XAML to the code-behind.
