---
title: Testing WCF Service Apps (Part 2 of 4)
id: 51
categories:
  - Uncategorized
date: 2008-11-29 17:35:00
tags:
---

Previous posts:    
[Part 0 of 4: Introduction](http://houseofbilz.com/HouseOfBilz/archive/2008/11/18/testing-wcf-service-apps-part-0-of-4.aspx)     
[Part 1 of 4: Testing the Service](http://houseofbilz.com/archive/2008/11/24/testing-wcf-service-apps-part-1-of-4.aspx)     
[![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2FHouseOfBilz%2Farchive%2F2008%2F11%2F18%2Ftesting-wcf-service-apps-part-0-of-4.aspx)](http://dotnetshoutout.com/Testing-WCF-Services) [![kick it on DotNetKicks.com](http://www.dotnetkicks.com/Services/Images/KickItImageGenerator.ashx?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2008%2f11%2f29%2ftesting-wcf-service-apps-part-2-of-4.aspx)](http://www.dotnetkicks.com/kick/?url=http%3a%2f%2fhouseofbilz.com%2farchive%2f2008%2f11%2f29%2ftesting-wcf-service-apps-part-2-of-4.aspx)

### **Testing the Client**

So far, I outlined how to test your WCF service.&#160; I simply took advantage or the WCF architecture and tested the service directly outside of the actual service harness.&#160; Now I need to set my sights on the client.&#160; This becomes a bit more difficult, but I wouldn't say that it is necessarily hard.&#160; I will start by giving a typical textbook example of hooking up to our service, and then I will tell you what is wrong with it.&#160; I will continue by modifying the code to be more testable so that the service can be mocked.&#160; 

### **A Textbook Example**

Most WCF tutorials and books have you start by adding a service reference to your service.&#160; Doing this will generate a proxy client that you can use in your application.&#160; My client application is a very simple data mining application.&#160; You can give it the ingredient name and the application will return all recipes that include the ingredient of choice.&#160; The first step is creating the service reference.&#160; Right-click on the project and select &quot;Add Service Reference&quot;.

[![AddServiceReference](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/TestingWCFServiceAppsPart2of4_8A59/AddServiceReference_thumb.gif)](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/TestingWCFServiceAppsPart2of4_8A59/AddServiceReference_2.gif) 

Once the service has been added, I can use it in my code:
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:cabbd96d-8ff9-4a13-a648-cb34f4477913" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> IngredientFinder
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> IEnumerable</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">RecipeData</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> GetRecipes(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> ingredientName)
    {
        var recipeService </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> RecipeBoxServiceClient();

        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> from recipe </span><span style="color: #0000FF;">in</span><span style="color: #000000;"> recipeService.AllRecipes() 
               </span><span style="color: #0000FF;">where</span><span style="color: #000000;"> recipe.ContainsIngredient(ingredientName) 
               select recipe;
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

This is all I need to get my app up and running. My main function just calls into this code and prints the results.&#160; There is, however, one major flaw with this code: **it is not testable!**&#160; This is because if I were to instantiate an instance of IngredientFinder, I would be required to hook up to a WCF service via the RecipeBoxServiceClient.&#160; Technically I could write this code but I wouldn't recommend it.&#160; For one, it requires a lot of setup to harness the service.&#160; Secondly, it drifts away from being a unit test and becomes more of a functional or integration test (more on this in part 4).&#160; Thirdly, you can't always assume that you control the service.&#160; Assume, for instance, that you are writing an application that connects to a service like Twitter.&#160; You certainly don't want your unit tests hitting the only instance of the service: the live one.

We need to do something about this...

### Making it Testable

If you were to inspect the RecipeBoxServiceClient class that was generated for you from the service specification, you would find one very important detail:&#160; RecipeBoxServiceClient implements the auto-generated interface IRecipeBoxService.&#160; Let us make a modification to the IngredientFinder to make it testable:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:d02efa0b-32d8-4786-861b-1ab4a13a10c3" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> IngredientFinder
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> IRecipeBoxService _recipeService;

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> IngredientFinder(IRecipeBoxService recipeService)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;">(recipeService </span><span style="color: #000000;">==</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">) </span><span style="color: #0000FF;">throw</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> ArgumentNullException(</span><span style="color: #800000;">"</span><span style="color: #800000;">recipeService</span><span style="color: #800000;">"</span><span style="color: #000000;">);

        _recipeService </span><span style="color: #000000;">=</span><span style="color: #000000;"> recipeService;
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> IEnumerable</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">RecipeData</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> GetRecipes(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> ingredientName)
    {
        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> from recipe </span><span style="color: #0000FF;">in</span><span style="color: #000000;"> _recipeService.AllRecipes() 
               </span><span style="color: #0000FF;">where</span><span style="color: #000000;"> recipe.ContainsIngredient(ingredientName) 
               select recipe;
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

This is a classic example of dependency injection.&#160; The user of the class (in our case, the main method) is now responsible for defining the service to use.&#160; This way, the IngredientFinder doesn't need to know anything about the connection details.&#160; In addition, I can write tests that mock out the service completely.

### **Writing Tests Against the Client**

In my example, I am using [Rhino Mocks](http://ayende.com/projects/rhino-mocks.aspx), but you can use any mock/fake/stub framework/method you wish. 

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:53be3351-f34e-4891-a8de-9b84d33b5c0a" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">[TestFixture]
</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> TestIngredientFinder
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> MockRepository _mocks;
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> IRecipeBoxService _mockService;
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> IngredientFinder _finder;

    [SetUp]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> SetUp()
    {
        _mocks </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> MockRepository();
        _mockService </span><span style="color: #000000;">=</span><span style="color: #000000;"> _mocks.CreateMock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IRecipeBoxService</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();
        _finder </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> IngredientFinder(_mockService);
    }

    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_IngredientFinder_With_Cheese()
    {
        Expect.Call(_mockService.AllRecipes()).
            Return(</span><span style="color: #0000FF;">new</span><span style="color: #000000;">[] {Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Macaroni</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">)});
        _mocks.ReplayAll();        

        var recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> _finder.GetRecipes(</span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">).ToArray();

        Assert.That(recipes.Length, Is.EqualTo(</span><span style="color: #800080;">1</span><span style="color: #000000;">));
        Assert.That(recipes[</span><span style="color: #800080;">0</span><span style="color: #000000;">].Title, Is.EqualTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    }

    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_IngredientFinder_With_Two_Recipes_That_Have_Cheese()
    {
        Expect.Call(_mockService.AllRecipes()).Return(</span><span style="color: #0000FF;">new</span><span style="color: #000000;">[]{
                                                              Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Macaroni</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">),
                                                              Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Grilled Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Bread</span><span style="color: #800000;">"</span><span style="color: #000000;">)
                                                          });
        _mocks.ReplayAll();

        var recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> _finder.GetRecipes(</span><span style="color: #800000;">"</span><span style="color: #800000;">cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">).ToArray();

        Assert.That(recipes.Length, Is.EqualTo(</span><span style="color: #800080;">2</span><span style="color: #000000;">));
        Assert.That(recipes[</span><span style="color: #800080;">0</span><span style="color: #000000;">].Title, Is.EqualTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">));
        Assert.That(recipes[</span><span style="color: #800080;">1</span><span style="color: #000000;">].Title, Is.EqualTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">Grilled Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    }

    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_IngredientFinder_Finding_Nothing()
    {
        Expect.Call(_mockService.AllRecipes()).Return(</span><span style="color: #0000FF;">new</span><span style="color: #000000;">[]{
                                                              Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Macaroni</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">),
                                                              Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Grilled Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Bread</span><span style="color: #800000;">"</span><span style="color: #000000;">)
                                                          });
        _mocks.ReplayAll();

        var recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> _finder.GetRecipes(</span><span style="color: #800000;">"</span><span style="color: #800000;">chicken</span><span style="color: #800000;">"</span><span style="color: #000000;">).ToArray();

        Assert.That(recipes.Length, Is.EqualTo(</span><span style="color: #800080;">0</span><span style="color: #000000;">));
    }

    [Test, ExpectedException(</span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(ArgumentNullException))]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_For_Null()
    {
        _finder.GetRecipes(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">);
    }

    [Test, ExpectedException(</span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(ArgumentNullException))]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_Constructor_With_Null_Service_Interface()
    {
        var junk </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> IngredientFinder(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">);
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

With this test suite, I have full coverage on my IngredientFinder class and I never needed to instantiate the actual service.&#160; 

### Next time

I will discuss how to test your client code with asynchronous service references.&#160; It turns out that it is not as straight-forward as the synchronous approach (this post), so I will devote an entire post to the asynchronous case. [(Part 3 of 4)](http://www.houseofbilz.com/archive/2008/12/19/testing-wcf-service-apps-part-3-of-4.aspx)