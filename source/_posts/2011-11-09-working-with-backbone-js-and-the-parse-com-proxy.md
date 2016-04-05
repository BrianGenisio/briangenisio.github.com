---
title: Working with Backbone.js and the Parse.com Proxy
date: 2011-11-09 11:32:19
layout: post
category: Software
tags: [CoffeeScript, Development, HTML5, JavaScript, Heroku, Backbone, Node.js, Parse.com, Technology, Web]
permalink: archives/2011/11/09/working-with-backbone-js-and-the-parse-com-proxy/
---

[Series Overview](/archives/2011/11/07/going-mostly-server-less-with-backbone-js/) 

[&lt;&lt; Making Backbone.js Work with Parse.com](/archives/2011/11/07/making-backbone-js-work-with-parse-com/)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[An Example Backbone/Parse.com App &gt;&gt;](/archives/2011/11/30/an-example-backboneparse-com-app/)

In the [last post](/archives/2011/11/07/making-backbone-js-work-with-parse-com/), I presented some base prototypes for Backbone.js models and collections that play nicely with Parse.com

Next, I will put all the pieces together and show how to use Backbone.js with Parse.com.&#160; All of the code is available on GitHub. [https://github.com/BrianGenisio/Parseback/tree/parse.com-proxy-with-backbone.js](https://github.com/BrianGenisio/Parseback/tree/parse.com-proxy-with-backbone.js)

### Create an “App” on Parse.com called “MyPeeps”

Once you crate the App, you will be given an **Application Id** and a **Master Key** which are essentially your user/password authentication for the site.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/ae906ffa-2f27-4317-a88f-7ff710850645/2011-11-04_22-10-11.png)![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/ec1bf2af-ed3d-4792-9bec-ea0ca1e71839/2011-11-04_22-10-47.png)

### Set the ApplicationID and MasterKey in the <font style="font-weight: bold">config.coffee </font>file

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/e2e751c7-91a1-41f1-9254-9036979675ac/2011-11-09_05-41-33.png)

### Create the model and collections (<font style="font-weight: bold">people.coffee</font>)

The model and collection extends from **ParseModel** and **ParseCollection** described in the [last post](/archives/2011/11/07/making-backbone-js-work-with-parse-com/).

```coffee
window.app = 
  models: {}
  collections: {}

class app.models.Person extends Backbone.ParseModel
  urlRoot: "/data/People"

class app.collections.People extends Backbone.ParseCollection
  model: app.models.Person
  url: "/data/People"
```

### Create <font style="font-weight: bold">index.html</font>
```html
<html>
<head>
  <script type="text/javascript" src="/vendor/jquery-1.6.2.js"></script>
  <script type="text/javascript" src="/vendor/underscore-1.1.7.js"></script>
  <script type="text/javascript" src="/vendor/backbone-0.5.3.js"></script>

  <script type="text/javascript" src="app.js"></script>
</head>
<body>
  Use the developer tools in your browser to play
</body>
</html>
```

### Run the [proxy server](/archives/2011/11/07/a-proxy-server-for-parse-com/)

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/8321bda4-3108-41b1-a228-62d255f2de75/2011-11-09_05-47-46.png)

### Point your browser to [http://localhost:3001](http://localhost:3001). Open your console and start playing

Use your developer tools to create and work with your backbone models (created in **people.coffee**).

### Create

Create a new **Person** model with the attributes you want and **save** it.&#160; The “People” class will get created on Parse.com and the attributes will get populated. Everything is schema-less so it gets created on demand.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/c4cc99a6-e50b-4ae3-930c-99f4e6db6bf4/2011-11-06_19-40-09.png)

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/a6bdfdb6-3f3c-4a9d-9786-90faf656dd06/2011-11-06_19-37-32.png)

### List

Create a new **People** collection and **fetch** the items.&#160; When the server responds, you will see your new item in the collection with the attributes you set.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/e2ca54e4-2b77-4a25-b8ca-e6f403451f13/2011-11-06_19-41-19.png)

### Update

When you update the attributes on your **Person** model and save it, the updates will go back to Parse.com.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/eb144cc0-ec82-47c9-8372-04889b828e08/2011-11-06_19-42-56.png)![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/7f640dff-9d2d-4687-b879-607aae7b7ebd/2011-11-06_19-43-17.png)

### Show

If you create a new **Person** model with a known id, you can **fetch** it from Parse.com

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/b2fa1cab-8ca6-48ca-a90b-8668264139c3/2011-11-06_19-45-05.png)

### Delete

If you don’t want your object anymore, you can **destroy** it.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/e0925b52-a9a1-4f75-9aa8-21f4b1834a04/2011-11-06_19-45-50.png)![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/12b4faae-523e-49c9-83a1-ef7f2dd51e9b/2011-11-06_19-46-07.png)

### Querying

A very useful feature of the Parse.com API is the ability to query data.&#160; To illustrate this, let’s populate the database with three **Person** models: Brian Genisio, Eli Genisio and Jon Doe.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/c78d171d-37e6-480b-93f2-76f2329692f5/2011-11-06_19-57-58.png)

I can pass a query parameter into the **fetch** function because **Person** derives from **ParseModel**.&#160; Here, I am querying for any object in the database with a **last** attribute of “Genisio”.&#160; Note that I only receive the two and Jon Doe is not retrieved.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/27fe7baa-aa3d-43db-9a04-afd576fdded4/2011-11-06_19-57-58.png)

Queries can be [more complex](https://www.parse.com/docs/rest#data-querying).&#160; Here is a similar query asking for all objects who’s **last** name attribute is not “Doe”.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/e58cc32d-228c-43c8-9bcd-46f21195929d/2011-11-06_19-59-13.png)

### Conclusion

I’ve illustrated how easy it is to use Backbone.js to work with the Parse.com backend.&#160; Everything available in the REST API is abstracted with the **Model** and **Collection** paradigm that Backbone provides.&#160; By placing a small proxy between your HTML client and Parse.com, you can quickly build single-paged applications and forget about the back-end. (For data-centric apps)

In my next post, I will give an [example of a single-paged application that uses Parse.com as the back-end.](/archives/2011/11/30/an-example-backboneparse-com-app/)