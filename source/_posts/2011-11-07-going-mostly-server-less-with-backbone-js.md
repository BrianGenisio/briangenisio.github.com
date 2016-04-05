---
title: Going (Mostly) Server-less with Backbone.js
date: 2011-11-07 11:19:36
layout: post
category: Software
tags: [JavaScript, CoffeeScript, Heroku, Development, HTML5, Backbone, Node.js, Parse.com, Technology, Web]
permalink: archives/2011/11/07/going-mostly-server-less-with-backbone-js/
---

I have been having a lot of fun with [Backbone.js](http://documentcloud.github.com/backbone/) and [CoffeeScript](http://jashkenas.github.com/coffee-script/) lately.&#160; It is my new programming obsession.&#160; They go together like chocolate and coffee.&#160; Along with this work, I have had the fantasy of going server-less and pushing all of my data stores to a back-end that I don’t have to code or manage myself.&#160; I mean, how cool would that be?&#160; To develop a browser-based application complete with data storage without a server is boss if you ask me.

For my data store, I have been playing with a new (currently in Beta) service called [Parse.com](https://www.parse.com/).&#160; Parse.com gives you a schema-less data store -- a place to put your stuff with a REST API.&#160; They are focusing on being the persistence layer for mobile apps when you don’t want to mess with a server.&#160; Is there any reason why we can’t use a service like this in an HTML5/Javascript single-paged app?&#160; It turns out that the answer is more complicated than “no” but it is something that we can certainly get around.&#160; 

This series will be broken into several parts:

1.  Introduction (this page)2.  [A Proxy Server for Parse.com](http://houseofbilz.com/archives/2011/11/07/a-proxy-server-for-parse-com/)3.  [Making Backbone.js Work with Parse.com](http://houseofbilz.com/archives/2011/11/07/making-backbone-js-work-with-parse-com/)4.  [Working with Backbone.js and the Parse.com Proxy](http://houseofbilz.com/archives/2011/11/09/working-with-backbone-js-and-the-parse-com-proxy/)5.  [Building an Application with Parse.com Models](http://houseofbilz.com/archives/2011/11/30/an-example-backboneparse-com-app/)6.  [Hosting the Todos example on Heroku (Bonus)](http://houseofbilz.com/archives/2011/11/30/hosting-the-todos-example-on-heroku/)  

All of the code (front-end and back-end) in these examples will be written in [CoffeeScript](http://jashkenas.github.com/coffee-script/) but there is no reason you can’t do the same thing with Javascript.&#160; Let me know if you find this short series useful.&#160; 

**Disclaimer:&#160; I am really just hacking and documenting my work.&#160; I have no idea how this will perform in a real-world scenario.&#160; It is a thought experiment.&#160; If anything, this is really just a place for me to document my work.**