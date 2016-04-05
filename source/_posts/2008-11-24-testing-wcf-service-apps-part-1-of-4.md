---
title: Testing WCF Service Apps (Part 1 of 4)
id: 50
categories:
  - Uncategorized
date: 2008-11-24 17:34:00
tags:
---

Previous posts: [Part 0 of 4: Introduction](http://houseofbilz.com/HouseOfBilz/archive/2008/11/18/testing-wcf-service-apps-part-0-of-4.aspx)
 [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2FHouseOfBilz%2Farchive%2F2008%2F11%2F18%2Ftesting-wcf-service-apps-part-0-of-4.aspx)](http://dotnetshoutout.com/Testing-WCF-Services)&#160;[![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2008%2f11%2f24%2ftesting-wcf-service-apps-part-1-of-4.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fgeekswithblogs.net%2fHouseOfBilz%2farchive%2f2008%2f11%2f24%2ftesting-wcf-service-apps-part-1-of-4.aspx)   

### **Testing the Service**

Of the four posts, testing the service is by far the easiest.

One of the most beautiful things about the WCF framework is the way it was designed to be more testable than ASPX services.&#160; When you design your WCF interface, you are mostly just designing an interface with the WCF ServiceContract attributes.&#160; The WCF framework uses your interface to determine the actual contract and transport mechanism so you don't have to.&#160; 

This is the key to testing WCF services:&#160; YOU DON'T HAVE TO WORRY ABOUT THE WCF FRAMEWORK.&#160; This means that you can simply instantiate your service directly and start calling public methods on it.&#160; You never need to worry about hitting the service through the transport layer.&#160; You can focus on what is important: your code.

### **Recipe Box Service Interface**

This is the interface that my WCF service publishes:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:fb50163d-8fc2-4d94-a74d-84fce328259c" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">[ServiceContract]
</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">interface</span><span style="color: #000000;"> IRecipeBoxService
{
    [OperationContract] 
    RecipeData[] AllRecipes();

    [OperationContract]
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> SaveRecipe(RecipeData recipe);

    [OperationContract]
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DeleteRecipe(</span><span style="color: #0000FF;">int</span><span style="color: #000000;"> id);

    [OperationContract]
    IngredientData[] AllIngredients();

    [OperationContract]
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> SaveIngredient(IngredientData ingredient);

    [OperationContract]
    IngredientData[] FindIngredients(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> nameIsLike);

    [OperationContract]  
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> DeleteIngredient(</span><span style="color: #0000FF;">int</span><span style="color: #000000;"> id);
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### **The Tests**

The actual implementation is named RecipeBoxService, and that is the class I am interested in testing.&#160;&#160; A test might look something like this:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:2ccaecdd-4f6e-4454-aab1-af9994bccea9" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">[Test]
</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_Get_All_Recipes_Returns_SingleRecipe()
{
    PopulateDatabase(</span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">AAA</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">BBB</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">CCC</span><span style="color: #800000;">"</span><span style="color: #000000;">));

    var recipeBoxService </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> RecipeBoxService();
    var recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> recipeBoxService.AllRecipes();

    Assert.That(recipes.Length, Is.EqualTo(</span><span style="color: #800080;">1</span><span style="color: #000000;">));
    Assert.That(recipes[</span><span style="color: #800080;">0</span><span style="color: #000000;">].Title, Is.EqualTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">AAA</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    Assert.That(recipes[</span><span style="color: #800080;">0</span><span style="color: #000000;">].Description, Is.EqualTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">BBB</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    Assert.That(recipes[</span><span style="color: #800080;">0</span><span style="color: #000000;">].Author, Is.EqualTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">CCC</span><span style="color: #800000;">"</span><span style="color: #000000;">));
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

It is that simple.&#160; YOU SHOULD NEVER NEED TO CREATE A SERVICE REFERENCE IN YOUR TEST PROJECT.&#160; Forget about WCF and test your service logic directly.

### **Abstracting the back end**

Note: I am using a database abstraction product called [Castle Active Record](http://castleproject.org/activerecord/index.html) in my service which gives me a lot of great features.&#160; From a testing perspective, the most important feature is that you can swap out the actual database for a more testable back end.&#160; Good unit tests (usually) do not rely on external databases, external services, file systems or any other environment-specific requirements.&#160; In my approach, I am using an in-memory, temporal database that only lives as long as the test does.&#160; Please see my post on [mocking out the database](http://www.houseofbilz.com/archive/2008/07/22/active-record-mock-framework.aspx) with ActiveRecord or more information on this. 

### Next time

I will talk about how you test the client without connecting to the service. [(Part 2 or 4)](http://houseofbilz.com/archive/2008/11/29/testing-wcf-service-apps-part-2-of-4.aspx)