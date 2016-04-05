---
title: Making Backbone.js Work with Parse.com
date: 2011-11-07 20:40:45
layout: post
category: Software
tags: [CoffeeScript, Development, HTML5, JavaScript, Heroku, Backbone, Node.js, Parse.com, Technology, Web]
permalink: archives/2011/11/07/making-backbone-js-work-with-parse-com/
---

[Series Overview](/archives/2011/11/07/going-mostly-server-less-with-backbone-js/)     
[&lt;&lt; A Proxy Server for Parse.com](/archives/2011/11/07/a-proxy-server-for-parse-com/)&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; [Working with Backbone.js and the Parse.com proxy &gt;&gt;](/archives/2011/11/09/working-with-backbone-js-and-the-parse-com-proxy/)

In the previous post, I showed how you can create a minimal server that proxies requests on to [Parse.com](http://parse.com) for back-end data storage.

Next, I need to consider the [Backbone.js](http://documentcloud.github.com/backbone/) side of things.&#160; Backbone gives a really great way of describing models and collections as long as the data conforms to its REST flavor.&#160; Unfortunately, Parse.com does things a little bit differently.&#160; I have encapsulated this code into some base prototypes that can be used later.&#160; 

**parse.com.coffee**
```coffee
class Backbone.ParseCollection extends Backbone.Collection
  parse: (resp, xhr) ->
    data = super
    data.results

  fetch: (options) ->
    if options?.query?
      options.data = "where=" + JSON.stringify options.query
      delete options.query
    super

class Backbone.ParseModel extends Backbone.Model
  setId: (data) ->
    data.id = data.objectId unless data.id
    data

  constructor: (model) ->
    @setId model
    super

  parse: (resp, xhr) ->
    @setId super

  toJSON: () ->
    result = super
    delete result.createdAt
    delete result.updatedAt
    result
```

These base classes help me to adapt the data and behavior of Parse.com:

### Collection Fetching

Backbone wants a JSON array from the server.&#160; Parse.com gives you a JSON object with an array called “results” inside the root object.&#160; The code overrides Backbone.Collection.parse to return the array property from the Parse.com response.

### Collection Queries

You can make server-side queries via the query parameters in the Parse.com API.&#160; The code overrides Backbone.Collection.fetch to take a “query” option.&#160; Mutate it to the format that Parse.com wants in the API.&#160; I’ll discuss this more in the next post.

### Model IDs

Parse.com IDs have the name of “objectId” but Backbone expects the IDs to be named “id”.&#160; The code overrides the Backbone.Model.parse and Backbone.Model.constructor functions to set the ID based on the “objectID” that comes from Parse.com.&#160; This will be used later when updating or deleting the object.

### Model Metadata

Parse.com includes two metadata fields: “createdAt” and “updatedAt”.&#160; While they may be useful, Parse.com fails if you send these fields via a PUT request.&#160; The code overrides Backbone.Model.toJSON to remove these items from the serialization, thus keeping them from being sent to Parse.com as well.

We now have base prototypes that allow us to communicate with the server.&#160; Instead of extending Backbone.Model and Backbone.Collection, you should make your models extend Backbone.ParseModel and Backbone.ParseCollection.&#160; 

In the next post, I will show how to do this and we will play in the browser a bit in order to show [how Backbone.js models and collections interact with the proxy server](/archives/2011/11/09/working-with-backbone-js-and-the-parse-com-proxy/). 

  