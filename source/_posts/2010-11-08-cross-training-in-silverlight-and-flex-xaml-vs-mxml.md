---
title: Cross-Training in Silverlight and Flex – XAML vs MXML
tags:
  - Cross-Training
  - Flex
  - MXML
  - Silverlight
  - XAML
id: 172
categories:
  - Uncategorized
date: 2010-11-08 04:02:47
---

More [Cross-Training in Silverlight and Flex](http://houseofbilz.com/archives/2010/10/24/cross-training-in-silverlight-vs-flex/)

[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2Farchives%2F2010%2F11%2F08%2Fcross-training-in-silverlight-and-flex-xaml-vs-mxml%2F)](http://dotnetshoutout.com/Cross-Training-in-Silverlight-and-Flex-XAML-vs-MXML)

Silverlight and Flex both include a declarative markup language for their UIs.&#160; Microsoft calls the Silverlight specification Extensible Application Markup Language (XAML pronounced zammel).&#160; Adobe calls the Flex specification MXML which is thought to have come from the original inventors: Macromedia extensible Markup Language.

In either case, these markup languages share two strong characteristics: They are both based on XML and they both do a fine job of declaring the UI.
  <div style="background-color: #e8f3ff">   

### XAML

You use XAML in Silverlight to define the user interface in a declarative way.&#160; XAML is nothing more than XML with some Silverilght namespaces that let you represent a visual tree.&#160; Following is an example of **MainPage.xaml**.
    <pre class="code"><span style="color: blue">&lt;</span><span style="color: #a31515">UserControl </span><span style="color: red">x</span><span style="color: blue">:</span><span style="color: red">Class</span><span style="color: blue">=&quot;Hello_World.MainPage&quot;
    </span><span style="color: red">xmlns</span><span style="color: blue">=&quot;http://schemas.microsoft.com/winfx/2006/xaml/presentation&quot;
    </span><span style="color: red">xmlns</span><span style="color: blue">:</span><span style="color: red">x</span><span style="color: blue">=&quot;http://schemas.microsoft.com/winfx/2006/xaml&quot;
             </span><span style="color: red">Width</span><span style="color: blue">=&quot;300&quot; </span><span style="color: red">Height</span><span style="color: blue">=&quot;36&quot;&gt;

    &lt;</span><span style="color: #a31515">Border </span><span style="color: red">BorderThickness</span><span style="color: blue">=&quot;5&quot; </span><span style="color: red">CornerRadius</span><span style="color: blue">=&quot;5&quot; </span><span style="color: blue">
            </span><span style="color: red">BorderBrush</span><span style="color: blue">=&quot;Black&quot; </span><span style="color: red">Height</span><span style="color: blue">=&quot;Auto&quot; </span><span style="color: red">Width</span><span style="color: blue">=&quot;Auto&quot;&gt;
        &lt;</span><span style="color: #a31515">StackPanel </span><span style="color: red">Orientation</span><span style="color: blue">=&quot;Horizontal&quot;&gt;
            &lt;</span><span style="color: #a31515">TextBox </span><span style="color: red">Width</span><span style="color: blue">=&quot;100&quot; </span><span style="color: red">Name</span><span style="color: blue">=&quot;Input&quot; </span><span style="color: red">Margin</span><span style="color: blue">=&quot;3&quot; /&gt;
            &lt;</span><span style="color: #a31515">Button </span><span style="color: red">Content</span><span style="color: blue">=&quot;Say Hello&quot; </span><span style="color: red">Click</span><span style="color: blue">=&quot;SayHello&quot; </span><span style="color: red">Margin</span><span style="color: blue">=&quot;3&quot; /&gt;
            &lt;</span><span style="color: #a31515">TextBlock </span><span style="color: red">Name</span><span style="color: blue">=&quot;Output&quot; </span><span style="color: red">Margin</span><span style="color: blue">=&quot;3&quot; <font color="#ff0000">VerticalAlignment</font>=&quot;Bottom&quot; /&gt;
        &lt;/</span><span style="color: #a31515">StackPanel</span><span style="color: blue">&gt;
    &lt;/</span><span style="color: #a31515">Border</span><span style="color: blue">&gt;

&lt;/</span><span style="color: #a31515">UserControl</span><span style="color: blue">&gt;
</span></pre>

The MainPage UserControl uses this XAML code to define the entire view.&#160; A Border houses three UI components stacked together: text input, a button to do something with and a label to show the output.&#160; If you were to type this into your MainPage control in Silverlight, it would look like this in your browser:

&#160;[![image](http://houseofbilz.com/wp-content/uploads/2010/11/image_thumb2.png "image")](http://houseofbilz.com/wp-content/uploads/2010/11/image2.png)

<span style="color: blue"></span>

Of course, the declarative XAML is nothing without some imperative behavior. Visual Studio, generates a corresponding “code-behind” whenever a UserControl is created. In the simplest of cases, you place your behavior in the “code-behind” file. In this case, the file is named **MainPage.xaml.cs**.&#160; By default, it creates a constructor for you that calls **InitializeComponent()**.&#160; I added the **SayHello** method that will get called whenever the button (defined in the XAML) is pressed.

  <pre class="code"><span style="color: blue">public partial class </span><span style="color: #2b91af">MainPage
</span>{
    <span style="color: blue">public </span>MainPage()
    {
        InitializeComponent();
    }

    <span style="color: blue">private void </span>SayHello(<span style="color: blue">object </span>sender, <span style="color: #2b91af">RoutedEventArgs </span>e)
    {
        Output.Text = <span style="color: #a31515">&quot;Hello &quot; </span>+ Input.Text;
    }
}</pre>

Most XAML introductions get off the train at this point. This series aims to show the differences between Silverlight and Flex so I will continue on to describe how the XAML and the C# code get glued together.

Notice in the “code behind” that the class it generated is a **partial** class.&#160; This means that there is more to this class somewhere else.&#160; Digging deeper into the **obj/Debug** folder, you will find an intermediate file named **MainPage.g.cs**.&#160; It holds the rest of this **partial** class:

  <pre class="code"><span style="color: blue">public partial class </span><span style="color: #2b91af">MainPage </span>: System.Windows.Controls.UserControl {

    <span style="color: blue">internal </span>System.Windows.Controls.TextBox Input;        
    <span style="color: blue">internal </span>System.Windows.Controls.TextBlock Output;        
    <span style="color: blue">private bool </span>_contentLoaded;

<span style="color: gray">    </span>[System.Diagnostics.<span style="color: #2b91af">DebuggerNonUserCodeAttribute</span>()]
    <span style="color: blue">public void </span>InitializeComponent() {
        <span style="color: blue">if </span>(_contentLoaded) {
            <span style="color: blue">return</span>;
        }
        _contentLoaded = <span style="color: blue">true</span>;
        System.Windows.Application.LoadComponent(<span style="color: blue">this</span>, 
            <span style="color: blue">new </span>System.Uri(<span style="color: #a31515">&quot;/Hello%20World;component/MainPage.xaml&quot;</span>, 
                           System.UriKind.Relative));
        <span style="color: blue">this</span>.Input = ((System.Windows.Controls.TextBox)(<span style="color: blue">this</span>.FindName(<span style="color: #a31515">&quot;Input&quot;</span>)));
        <span style="color: blue">this</span>.Output = ((System.Windows.Controls.TextBlock)(<span style="color: blue">this</span>.FindName(<span style="color: #a31515">&quot;Output&quot;</span>)));
    }
}</pre>

We can see where some of the important “glue” is defined. The code derives from **UserControl** and defines the **InitializeComponent** method that is called in the constructor.&#160; It also generates the two named controls: **Input** and** Outut **based on the names defined in the XAML.&#160; The **InitializeComponent** method then desterilizes the XAML into an object tree.&#160; It finishes by setting the **Input** and **Output** controls to be items found in the visual tree.&#160; This is how the “code-behind” can access these controls in the **SayHello** method.

The last bit of glue happens in the compiler.&#160; The XAML file is compiled into the DLL as an “embedded resource”.&#160; This happens automatically so you don’t need to think about it.&#160; If you are curious, you can crack open the DLL with [Reflecor](http://www.red-gate.com/products/reflector/index.htm) and find the XAML packed into the DLL.&#160; The **InitializeComponent** method loads the tree from the same XAML file which is embedded at compile-time.

[](http://11011.net/software/vspaste)

[![Untitled](http://houseofbilz.com/wp-content/uploads/2010/11/Untitled_thumb.gif "Untitled")](http://houseofbilz.com/wp-content/uploads/2010/11/Untitled.gif)&#160;

</div>

<div style="background-color: #ffe8e8">

### MXML

MXML in Flex is used to define the user interface in a declarative way.&#160; MXML is nothing more than XML with some Flex namespaces that let you represent a visual tree.&#160; Following is an example of **Main.mxml**.

<font size="2"><font face="Courier New">&lt;?xml version=<font color="#ff0000">&quot;1.0&quot;</font> encoding=<font color="#ff0000">&quot;utf-8&quot;</font>?&gt;

<font color="#0000ff">&lt;s:Application</font> xmlns:fx=<font color="#ff0000">&quot;</font></font></font>[<font color="#ff0000" size="2" face="Courier New">http://ns.adobe.com/mxml/2009&quot;</font>](http://ns.adobe.com/mxml/2009&quot;)

<font size="2" face="Courier New">&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; xmlns:s=<font color="#ff0000">&quot;library://ns.adobe.com/flex/spark&quot;</font>

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; width=<font color="#ff0000">&quot;300&quot;</font> height=<font color="#ff0000">&quot;40&quot;</font><font color="#0000ff">&gt;

</font>&#160;&#160; </font><font size="2"><font face="Courier New"><font color="#008000">&lt;fx:Script&gt;

</font>&#160;&#160;&#160;&#160;&#160;&#160; &lt;![CDATA[

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; <font color="#0000ff">public function</font> sayHello():<font color="#0000ff">void</font>{

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; output.text = <font color="#ff0000">&quot;Hello &quot;</font>+ input.text;

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; }

&#160;&#160;&#160;&#160;&#160;&#160;&#160; ]]&gt;

&#160;&#160; <font color="#008000">&lt;/fx:Script&gt;</font> </font></font>

<font size="2" face="Courier New">&#160;&#160; <font color="#0000ff">&lt;s:BorderContainer</font> borderWeight=<font color="#ff0000">&quot;5&quot;</font> cornerRadius=<font color="#ff0000">&quot;5&quot;</font>

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; borderColor=<font color="#ff0000">&quot;black&quot;</font> minHeight=<font color="#ff0000">&quot;0&quot;</font> width=<font color="#ff0000">&quot;100%&quot;</font><font color="#0000ff">&gt;</font>

&#160;&#160;&#160;&#160;&#160;&#160;&#160; <font color="#0000ff">&lt;s:HGroup</font> left=<font color="#ff0000">&quot;3&quot;</font> right=<font color="#ff0000">&quot;3&quot;</font> top=<font color="#ff0000">&quot;3&quot;</font> bottom=<font color="#ff0000">&quot;3&quot;</font><font color="#0000ff">&gt;</font>

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; <font color="#0000ff">&lt;s:TextInput</font> width=<font color="#ff0000">&quot;100&quot;</font> id=<font color="#ff0000">&quot;input&quot;</font>/<font color="#0000ff">&gt;</font>

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; <font color="#0000ff">&lt;s:Button</font> label=<font color="#ff0000">&quot;Say Hello&quot;</font> click=<font color="#ff0000">&quot;sayHello()&quot;</font> left=<font color="#ff0000">&quot;6&quot;</font>/<font color="#0000ff">&gt;</font>

&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; <font color="#0000ff">&lt;s:Label</font> id=<font color="#ff0000">&quot;output&quot;</font> left=<font color="#ff0000">&quot;6&quot;</font> verticalAlign=<font color="#ff0000">&quot;bottom&quot;</font> height=<font color="#ff0000">&quot;100%&quot;</font>/<font color="#0000ff">&gt;</font>

&#160;&#160;&#160;&#160;&#160;&#160;&#160; </font><font color="#0000ff" size="2" face="Courier New">&lt;/s:HGroup&gt;

&#160;&#160;&#160; &lt;/s:BorderContainer&gt;

&lt;/s:Application&gt;</font>

<span style="color: blue"><font color="#000000">This MXML code defines the entire application.&#160; It is composed of a **BorderContainer** that houses three UI components grouped together: text input, a button to do something with and a label to show the output.&#160; If you were to type this into your **Main.mxml** view in Flex, it would look like this in your browser:</font></span>

[![image](http://houseofbilz.com/wp-content/uploads/2010/11/image_thumb3.png "image")](http://houseofbilz.com/wp-content/uploads/2010/11/image3.png)

Of course, the declarative MXML is nothing without some imperative behavior.&#160; The default way to do this in Flex is to use the HTML model of **Script** tags.&#160; The **sayHello** method is defined directly in the MXML.&#160; There are better ways (in my opinion) to separate the imperative behavior from the view, but this is a simple example.

Most MXML introductions get off the train at this point.&#160; This series aims to show the differences between Silverlight and Flex, so I will continue to describe what happens to this MXML at compile-time.

Unlike Silverlight where the XAML is embedded in the compiled project and parsed at runtime, Flex takes a different approach.&#160; MXML views get parsed at compile-time and translated directly into ActionScript. The actual code that the compiler generates is transient – it gets removed after the compile step -- unless you add the –**keep-generated-actionscript** flag to the compiler.

When this happens, quite a lot of code gets generated.&#160; I don’t want to put everything on this post, but here is a snippet from the generated code:

<font size="2"><font face="Courier New"><font color="#0000ff">public</font> <font color="#ff00ff">class</font> Main <font color="#0000ff">extends</font> spark.components.Application&#160;&#160;&#160; 
{</font> </font>

&#160;&#160;&#160; [<font color="#0000ff">Bindable</font>] <font color="#0000ff">public</font> <font color="#8bb8ba">var</font> input : spark.components.TextInput;&#160;&#160;&#160; 
&#160;&#160;&#160; [<font color="#0000ff">Bindable</font>] <font color="#0000ff">public</font> <font color="#6ca6aa">var</font> output : spark.components.Label;

&#160;&#160;&#160; <font color="#0000ff">public</font> <font color="#008000">function</font> Main()

&#160;&#160;&#160; {

&#160;&#160;&#160;&#160;&#160;&#160;&#160; <font color="#0000ff">super</font>();

&#160;&#160;&#160;&#160;&#160;&#160;&#160; <font color="#0000ff">this</font>.width = 300;

&#160;&#160;&#160;&#160;&#160;&#160;&#160; <font color="#0000ff">this</font>.height = 40;

&#160;&#160;&#160;&#160;&#160;&#160;&#160; <font color="#0000ff">this</font>.mxmlContentFactory = <font color="#0000ff">new </font>mx.core.DeferredInstanceFromFunction(_Main_Array1_c);

&#160;&#160;&#160; }

}

The constructor sets up a **_Main_Array1_c** function which calls several other methods hierarchically which creates the controls that were defined in the MXML.

The details of the generated code are not terribly important.&#160; The important thing to understand is that the MXML gets compiled into ActionScript and it is not parsed at run-time.&#160; XAML in Silverlight gets parsed at run-time but VisualStudio does generate some C# code to help glue the XAML to the code-behind.

</div>