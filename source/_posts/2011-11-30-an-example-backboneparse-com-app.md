---
title: An Example Backbone/Parse.com App
date: 2011-11-30 20:02:15
layout: post
category: Software
tags: [CoffeeScript, Development, HTML5, JavaScript, Heroku, Backbone, Node.js, Parse.com, Technology, Web]
permalink: archives/2011/11/30/an-example-backboneparse-com-app/
---

[Series Overview](/archives/2011/11/07/going-mostly-server-less-with-backbone-js/)

[&lt;&lt; Working with Backbone.js and the Parse.com Proxy](/archives/2011/11/09/working-with-backbone-js-and-the-parse-com-proxy/)&nbsp;&nbsp;&nbsp;&nbsp;[Hosting the Todos example on Heroku &gt;&gt;](/archives/2011/11/30/hosting-the-todos-example-on-heroku/)

This is the post where I put it all together.&#160; I showed how to create a proxy to [use Parse.com as your data store](/archives/2011/11/07/a-proxy-server-for-parse-com/).&#160; I also showed how to [modify Backbone.js to use Parse.com](/archives/2011/11/07/making-backbone-js-work-with-parse-com/).&#160; Now, I will show how easy it is to use Backbone.js models and collections with a service like Parse.com.&#160; Note that Parse.com is not the only service out there that does this.&#160; StackMob is another example of a “place to put your stuff” service.&#160; There are others.

In this app, I will borrow from the default [Backbone.js](http://documentcloud.github.com/backbone/) example: Todos.&#160; [Todos](http://documentcloud.github.com/backbone/#examples-todos) is a simple to-do list, which uses the browser’s local storage to save data.&#160; 
![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/f0dd85a2-2d24-43c6-bb73-1319e835105a/2011-11-30_14-22-43.png)

I will adapt it to be hosted by the Parse.com proxy that I showed in my previous post.&#160; Instead of storing locally, the app will put push to Parse.com.&#160; Want to play with it?&#160; I have hosted everything on Heroku: [http://todos-parsepack.herokuapp.com/](http://todos-parsepack.herokuapp.com/)

There are only a few tiny steps to make this work:

1.  Replace localstorage.js with parse.com.js
2.  Modify the model and collection to use Parse.com instead of local storage
3.  Configure the proxy
4.  Run the proxy

&#160;

### 1\. Modify index.html

The example given with Backbone.js uses the “localstorage” override for Backbone.sync.&#160; We want to use the default REST version Backbone.sync so we remove it.&#160; We also want to use the base prototypes to adapt Parse.com to Backbone.

<font face="Courier New"><strike>&lt;script src=&quot;backbone-localstorage.js&quot;&gt;&lt;/script&gt; 
</strike></font>

```html
<script src=”parse.com.js"></script>;
```

### 2a. Modify the model

The example uses a vanilla Backbone.Model.&#160; Instead, change it to Backbone.ParseModel and give it the location of the data.

<font face="Courier New"><strike>window.Todo = Backbone.Model.extend({ </strike></font>

```javascript
window.Todo = Backbone.ParseModel.extend({
    urlRoot: "/data/Todos",
```


### 2b. Modify the Collection

The example uses a Backbone.Collection.&#160; Instead, change it to Backbone.ParseCollection and give it the location of the data.

<font face="Courier New"><strike>window.TodoList = Backbone.Collection.extend({ 

</strike></font>

```javascript
window.TodoList = Backbone.ParseCollection.extend({
    url: "/data/Todos",
```

Also, since we aren’t using local storage, remove the localStore details.

<font face="Courier New"><strike>localStorage: new Store(&quot;todos&quot;),</strike></font>

### 3\. Configure the App

That is all you need to do in order to modify the app.&#160; Everything else is exactly the same.&#160; You do, however, need to create an App at Parse.com. 

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/829dc2f5-dca8-48b9-8c0a-0b59879a0504/2011-11-30_14-34-18.png)![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/60446bb8-193a-4be8-a359-e5ab433f6213/2011-11-30_14-35-47.png)

Use the Application ID and Master Key and edit the config.coffee file:

```coffee
applicationID: 'APPLICATION ID' 
masterKey:    'MASTER KEY' 
apiPath:  '/data/'
port:     3001
```


### 4\. Run the proxy:

```bash
> coffee proxy.coffee
```

### Test the app

Add items to your list.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/f1e9ed51-045a-43a2-8afa-ae00514650a0/2011-11-30_14-40-41.png)

&#160;

See them show up in your data store.

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/51f7e2c9-2191-4bde-9c6f-952bb816519e/2011-11-30_14-44-14.png)

### Conclusion

That was insanely easy.&#160; You just need to tell you models and collections to go to the server instead of local storage, configure the proxy and run it.&#160; This solution installs well on Heroku as you can see.&#160; This app wasn’t built with any type of user models, so everyone in the world gets to share Todos in this example… that can also be done with Parse.com, but I that is a different post ☺.

If anyone finds this useful, I would be curious to know if it helps.&#160;  If you want the source code for this project, take a look at [the GitHub project](https://github.com/BrianGenisio/todos-parseback) 

As a bonus, I wrote up the modifications I needed to make in order to [host the Parse-backed Todos example on Heroku](/archives/2011/11/30/hosting-the-todos-example-on-heroku/).  It was really easy and worth the writeup. 