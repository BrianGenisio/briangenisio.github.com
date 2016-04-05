---
title: Testing WCF Service Apps (Part 3 of 4)
id: 53
categories:
  - Uncategorized
date: 2008-12-19 17:37:00
tags:
---

Previous Posts:    
[Part 0 of 4: Introduction](http://houseofbilz.com/HouseOfBilz/archive/2008/11/18/testing-wcf-service-apps-part-0-of-4.aspx)     
[Part 1 of 4: Testing the Service](http://houseofbilz.com/archive/2008/11/24/testing-wcf-service-apps-part-1-of-4.aspx)     
[Part 2 of 4: Testing the Client](http://www.houseofbilz.com/archive/2008/11/29/testing-wcf-service-apps-part-2-of-4.aspx)
 [![Shout it](http://dotnetshoutout.com/image.axd?url=http%3A%2F%2Fhouseofbilz.com%2FHouseOfBilz%2Farchive%2F2008%2F11%2F18%2Ftesting-wcf-service-apps-part-0-of-4.aspx)](http://dotnetshoutout.com/Testing-WCF-Services)   

### Testing Asynchronous Clients

Up to this point, we have tested the service and we have tested the client -- both in isolation.&#160; We have written unit tests and our code&#160; has good coverage.&#160; Unfortunately, my clients are not always synchronous.&#160; In Silverlight client, for instance, the framework will not permit you to make synchronous service requests.&#160; As it turns out, writing tests for asynchronous service clients is not straight-forward.&#160; Thankfully, there are some hacks that you can take advantage of to write effective asynchronous tests.

### Generating Asynchronous Service Clients

When you generate your service reference in Visual Studio, the advanced options allow you to specify an asynchronous proxy.&#160; 

[![Async](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/TestingWCFServiceAppsPart3of4_12EF1/Async_thumb.png)](http://geekswithblogs.net/images/geekswithblogs_net/HouseOfBilz/WindowsLiveWriter/TestingWCFServiceAppsPart3of4_12EF1/Async_2.png) 

In a textbook example, your is a bit more complicated but it is still pretty easy to follow.&#160; Instead of calling **AllRecipes** (as in the previous post), the class hooks the **AllRecipesCompleted** event and calls **AllRecipesAsync()**.&#160; When the service returns the results, the event is fired and the results are processed.
  <div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:ae876ec9-bb83-4eaf-94a7-35cc8c59e8d7" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> IngredientFinder
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">event</span><span style="color: #000000;"> EventHandler</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IngredentFinderCompleteArgs</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> ProcessingComplete;
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> _ingredientName;

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> FindRecipes(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> ingredientName)
    {
        var recipeService </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> RecipeBoxServiceClient();

        _ingredientName </span><span style="color: #000000;">=</span><span style="color: #000000;"> ingredientName;
        recipeService.AllRecipesCompleted </span><span style="color: #000000;">+=</span><span style="color: #000000;"> AllRecipes_Completed;
        recipeService.AllRecipesAsync();
    }

    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> AllRecipes_Completed(</span><span style="color: #0000FF;">object</span><span style="color: #000000;"> sender, AllRecipesCompletedEventArgs e)
    {
        var recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> from recipe </span><span style="color: #0000FF;">in</span><span style="color: #000000;"> e.Result
                      </span><span style="color: #0000FF;">where</span><span style="color: #000000;"> recipe.ContainsIngredient(_ingredientName)
                      select recipe;

        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (ProcessingComplete </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
            ProcessingComplete(</span><span style="color: #0000FF;">this</span><span style="color: #000000;">, </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> IngredentFinderCompleteArgs { Recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> recipes });
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### Making it Testable

Just like in [part 2](http://www.houseofbilz.com/archive/2008/11/29/testing-wcf-service-apps-part-2-of-4.aspx), the code works great but it is not at all testable.&#160; **IngredientFinder** is tightly coupled to the **RecipeBoxServiceClient**.&#160; In a unit testing environment, you cannot rely on WCF to host the service.&#160; Unlike part 2, it is not as simple as replacing the concrete service with **IRecipeBoxService**.&#160; The interface that is generated looks like this:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:86030e47-7127-4e90-9dd9-085a835e5587" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">interface</span><span style="color: #000000;"> IRecipeBoxService
{
    RecipeData[] AllRecipes();
    IAsyncResult BeginAllRecipes(AsyncCallback callback, </span><span style="color: #0000FF;">object</span><span style="color: #000000;"> asyncState);
    RecipeData[] EndAllRecipes(IAsyncResult result);
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

I have no idea why Microsoft did this, but the interface does not include the interface that the concrete class implements.&#160; Neither the **AllRecipesCompleted** event or the **AllRecipesAsync** method are found in the interface!&#160; It includes the begin/end calls but those are some messy methods to use.&#160; I don't want to require my **IngredientFinder** class to be required to use messy methods just to make my code testable.&#160; This is where my hack comes in.&#160; It takes advantage of the fact that the **RecipeBoxServiceClient** is a partial class:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:6e1e6c7b-4dd6-410e-b069-4090e61e0bf9" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">interface</span><span style="color: #000000;"> IRecipeBoxServiceAsync : IRecipeBoxService
{
    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> AllRecipesAsync();
    </span><span style="color: #0000FF;">event</span><span style="color: #000000;"> System.EventHandler</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">AllRecipesCompletedEventArgs</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> AllRecipesCompleted;
}

</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">partial</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> RecipeBoxServiceClient : IRecipeBoxServiceAsync
{}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

So what did I do here?&#160; I created a new, asynchronous interface that includes the **IRecipeBoxService** interface and also includes the event and async method that is implemented in the concrete class.&#160; After that, I tell the class to implement the asynchronous interface via the partial keyword.&#160; I don't have to write any implementation code because it has already been done for me -- the interface simply didn't include it.

Now that this is in place, we can modify the class slightly to pass the interface in via dependency injection:

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:5283173b-9af7-4595-a72e-1a756f151436" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> IngredientFinder
{
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">event</span><span style="color: #000000;"> EventHandler</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IngredentFinderCompleteArgs</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> ProcessingComplete;

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">readonly</span><span style="color: #000000;"> IRecipeBoxServiceAsync _recipeService;
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">string</span><span style="color: #000000;"> _ingredientName;

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> IngredientFinder(IRecipeBoxServiceAsync service)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (service </span><span style="color: #000000;">==</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">) </span><span style="color: #0000FF;">throw</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> ArgumentNullException(</span><span style="color: #800000;">"</span><span style="color: #800000;">service</span><span style="color: #800000;">"</span><span style="color: #000000;">);

        _recipeService </span><span style="color: #000000;">=</span><span style="color: #000000;"> service;
    }

    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> FindRecipes(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> ingredientName)
    {
        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (</span><span style="color: #0000FF;">string</span><span style="color: #000000;">.IsNullOrEmpty(ingredientName)) </span><span style="color: #0000FF;">throw</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> ArgumentNullException(</span><span style="color: #800000;">"</span><span style="color: #800000;">ingredientName</span><span style="color: #800000;">"</span><span style="color: #000000;">);

        _ingredientName </span><span style="color: #000000;">=</span><span style="color: #000000;"> ingredientName;
        _recipeService.AllRecipesCompleted </span><span style="color: #000000;">+=</span><span style="color: #000000;"> AllRecipes_Completed;
        _recipeService.AllRecipesAsync();
    }

    </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> AllRecipes_Completed(</span><span style="color: #0000FF;">object</span><span style="color: #000000;"> sender, AllRecipesCompletedEventArgs e)
    {
        var recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> from recipe </span><span style="color: #0000FF;">in</span><span style="color: #000000;"> e.Result
                      </span><span style="color: #0000FF;">where</span><span style="color: #000000;"> recipe.ContainsIngredient(_ingredientName)
                      select recipe;

        </span><span style="color: #0000FF;">if</span><span style="color: #000000;"> (ProcessingComplete </span><span style="color: #000000;">!=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">)
            ProcessingComplete(</span><span style="color: #0000FF;">this</span><span style="color: #000000;">, </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> IngredentFinderCompleteArgs { Recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> recipes });
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### The Tests

The tests are also more complex.&#160; The test needs to simulate an asynchronous service and fire events.&#160; I am using [Moq](http://code.google.com/p/moq/) to mock out the service.

<div style="padding-bottom: 0px; margin: 0px; padding-left: 0px; padding-right: 0px; display: inline; float: none; padding-top: 0px" id="scid:57F11A72-B0E5-49c7-9094-E3A15BD5B5E6:a2b3d153-9f5c-4bae-953c-c9b86b7728d1" class="wlWriterEditableSmartContent"><pre style="background-color:#E4E4E4;overflow: auto;"><span style="color: #000000;">[TestFixture]
</span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">class</span><span style="color: #000000;"> TestIngredientFinder
{
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IRecipeBoxServiceAsync</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> _mockService;
    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> IngredientFinder _finder;

    [SetUp]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> SetUp()
    {
        _mockService </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IRecipeBoxServiceAsync</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">(MockBehavior.Strict);
        _finder </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> IngredientFinder(_mockService.Object);
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> RecipeData Recipe(</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> recipeName, </span><span style="color: #0000FF;">params</span><span style="color: #000000;"> </span><span style="color: #0000FF;">string</span><span style="color: #000000;">[] ingredientNames)
    {
        var result </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> RecipeData {Title </span><span style="color: #000000;">=</span><span style="color: #000000;"> recipeName};

        var quantities </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> List</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">QuantityData</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();
        </span><span style="color: #0000FF;">foreach</span><span style="color: #000000;"> (</span><span style="color: #0000FF;">string</span><span style="color: #000000;"> ingredientName </span><span style="color: #0000FF;">in</span><span style="color: #000000;"> ingredientNames)
            quantities.Add(</span><span style="color: #0000FF;">new</span><span style="color: #000000;"> QuantityData{Ingredient </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> IngredientData{ Name </span><span style="color: #000000;">=</span><span style="color: #000000;"> ingredientName}});

        result.Quantities </span><span style="color: #000000;">=</span><span style="color: #000000;"> quantities.ToArray();
        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> result;
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> AllRecipesCompletedEventArgs RecipeCompletedArgs(</span><span style="color: #0000FF;">params</span><span style="color: #000000;"> RecipeData[] results)
    {
        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> AllRecipesCompletedEventArgs(</span><span style="color: #0000FF;">new</span><span style="color: #000000;"> </span><span style="color: #0000FF;">object</span><span style="color: #000000;">[] {results}, </span><span style="color: #0000FF;">null</span><span style="color: #000000;">, </span><span style="color: #0000FF;">false</span><span style="color: #000000;">, </span><span style="color: #0000FF;">null</span><span style="color: #000000;">);
    }

    </span><span style="color: #0000FF;">private</span><span style="color: #000000;"> </span><span style="color: #0000FF;">static</span><span style="color: #000000;"> MockedEvent</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">AllRecipesCompletedEventArgs</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> RegisterCompletedHandler(Mock</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">IRecipeBoxServiceAsync</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> mockService)
    {
        var serviceCompletedHandler </span><span style="color: #000000;">=</span><span style="color: #000000;"> mockService.CreateEventHandler</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">AllRecipesCompletedEventArgs</span><span style="color: #000000;">&gt;</span><span style="color: #000000;">();
        mockService.Object.AllRecipesCompleted </span><span style="color: #000000;">+=</span><span style="color: #000000;"> serviceCompletedHandler;
        </span><span style="color: #0000FF;">return</span><span style="color: #000000;"> serviceCompletedHandler;
    }

    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_IngredientFinder_With_One_Recipe_That_Has_Cheese()
    {
        var serviceCompletedHandler </span><span style="color: #000000;">=</span><span style="color: #000000;"> RegisterCompletedHandler(_mockService);
        _mockService.Expect(service </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> service.AllRecipesAsync());

        IEnumerable</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">RecipeData</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">;
        _finder.ProcessingComplete </span><span style="color: #000000;">+=</span><span style="color: #000000;"> (sender, args) </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> args.Recipes;

        _finder.FindRecipes(</span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        serviceCompletedHandler.Raise(RecipeCompletedArgs(Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Macaroni</span><span style="color: #800000;">"</span><span style="color: #000000;">)));

        Assert.That(recipes.Count(), Is.EqualTo(</span><span style="color: #800080;">1</span><span style="color: #000000;">));
        Assert.That(recipes.ToList()[</span><span style="color: #800080;">0</span><span style="color: #000000;">].Title, Is.EqualTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    }

    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_IngredientFinder_With_Two_Recipes_That_Have_Cheese()
    {
        var serviceCompletedHandler </span><span style="color: #000000;">=</span><span style="color: #000000;"> RegisterCompletedHandler(_mockService);
        _mockService.Expect(service </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> service.AllRecipesAsync());

        IEnumerable</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">RecipeData</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">;
        _finder.ProcessingComplete </span><span style="color: #000000;">+=</span><span style="color: #000000;"> (sender, args) </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> args.Recipes;

        _finder.FindRecipes(</span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        serviceCompletedHandler.Raise(RecipeCompletedArgs(
                                          Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Macaroni</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">),
                                          Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Grilled Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Bread</span><span style="color: #800000;">"</span><span style="color: #000000;">)));

        Assert.That(recipes.Count(), Is.EqualTo(</span><span style="color: #800080;">2</span><span style="color: #000000;">));
        Assert.That(recipes.ToList()[</span><span style="color: #800080;">0</span><span style="color: #000000;">].Title, Is.EqualTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">));
        Assert.That(recipes.ToList()[</span><span style="color: #800080;">1</span><span style="color: #000000;">].Title, Is.EqualTo(</span><span style="color: #800000;">"</span><span style="color: #800000;">Grilled Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">));
    }

    [Test]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_IngredientFinder_Finding_Nothing()
    {
        var serviceCompletedHandler </span><span style="color: #000000;">=</span><span style="color: #000000;"> RegisterCompletedHandler(_mockService);
        _mockService.Expect(service </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> service.AllRecipesAsync());

        IEnumerable</span><span style="color: #000000;">&lt;</span><span style="color: #000000;">RecipeData</span><span style="color: #000000;">&gt;</span><span style="color: #000000;"> recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">null</span><span style="color: #000000;">;
        _finder.ProcessingComplete </span><span style="color: #000000;">+=</span><span style="color: #000000;"> (sender, args) </span><span style="color: #000000;">=&gt;</span><span style="color: #000000;"> recipes </span><span style="color: #000000;">=</span><span style="color: #000000;"> args.Recipes;

        _finder.FindRecipes(</span><span style="color: #800000;">"</span><span style="color: #800000;">chicken</span><span style="color: #800000;">"</span><span style="color: #000000;">);
        serviceCompletedHandler.Raise(RecipeCompletedArgs(
                                          Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Mac&amp;Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Macaroni</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">),
                                          Recipe(</span><span style="color: #800000;">"</span><span style="color: #800000;">Grilled Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Cheese</span><span style="color: #800000;">"</span><span style="color: #000000;">, </span><span style="color: #800000;">"</span><span style="color: #800000;">Bread</span><span style="color: #800000;">"</span><span style="color: #000000;">)));

        Assert.That(recipes.Count(), Is.EqualTo(</span><span style="color: #800080;">0</span><span style="color: #000000;">));
    }

    [Test, ExpectedException(</span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(ArgumentNullException))]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_For_Null()
    {
        _finder.FindRecipes(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">);
    }

    [Test, ExpectedException(</span><span style="color: #0000FF;">typeof</span><span style="color: #000000;">(ArgumentNullException))]
    </span><span style="color: #0000FF;">public</span><span style="color: #000000;"> </span><span style="color: #0000FF;">void</span><span style="color: #000000;"> Test_Constructor_With_Null_Service_Interface()
    {
        var junk </span><span style="color: #000000;">=</span><span style="color: #000000;"> </span><span style="color: #0000FF;">new</span><span style="color: #000000;"> IngredientFinder(</span><span style="color: #0000FF;">null</span><span style="color: #000000;">);
    }
}</span></pre><!-- Code inserted with Steve Dunn's Windows Live Writer Code Formatter Plugin.  http://dunnhq.com --></div>

### Next Time

In the last post of this series, I will discuss functional testing.&#160; How do you test the round-trip functionality of your application?&#160; I will re-visit the synchronous client and show some tricks that allow you to test all points of your application without requiring the WCF infrastructure to be running. [(part 4 of 4)](http://www.houseofbilz.com/archive/2009/01/05/testing-wcf-service-apps-part-4-of-4.aspx)