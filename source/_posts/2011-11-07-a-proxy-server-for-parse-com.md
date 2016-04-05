---
title: A Proxy Server for Parse.com
tags:
  - CoffeeScript
  - Development
  - HTML5
  - Javascript
  - Node.js
  - Parse.com
  - Technology
  - Web
id: 366
categories:
  - Uncategorized
date: 2011-11-07 11:41:46
---

[&lt;&lt; See the full series of posts](http://houseofbilz.com/archives/2011/11/07/going-mostly-server-less-with-backbone-js/)&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; [Making Backbone.js work with Parse.com &gt;&gt;](http://houseofbilz.com/archives/2011/11/07/making-backbone-js-work-with-parse-com/)

In my [previous post](http://houseofbilz.com/archives/2011/11/07/going-mostly-server-less-with-backbone-js/), I asked the question: “Is there any reason why can’t we use a service like [[Parse.com](https://www.parse.com/)] in an HTML5/Javascript single-paged app?”&#160; The answer is “yes”.

The reason: [the same origin policy](http://en.wikipedia.org/wiki/Same_origin_policy).&#160; The browser won’t let you request Parse.com data from a different domain.&#160; There are certainly tricks you can play if you just want to GET the data (like JSONP for example), but there is no good way to make POST/PUT/DELETE requests.&#160; You have to stay on your own domain if you want to use all that a REST API has to offer.

Using my newfound [CoffeeScript](http://jashkenas.github.com/coffee-script/) skills, I came up with this proxy server based on [Node.js](http://nodejs.org/) with [Express](http://expressjs.com/) and [Restler](https://github.com/danwrong/restler).&#160; While I’m at it, the server can return my index.html and all other assets (images, Javascript, CSS and HTML templates). 

Here is the code (full code for this post is available on GitHub [https://github.com/BrianGenisio/Parseback/tree/parse.com-proxy-with-readme](https://github.com/BrianGenisio/Parseback/tree/parse.com-proxy-with-readme)):

**proxy.coffee**
  <pre class="brush: coffeescript; toolbar: false;">express = require 'express'
restler = require 'restler'
config = require './config'

app = express.createServer()
app.use express.bodyParser()

app.get &quot;/&quot;, (req, res) =&gt;
  res.sendfile &quot;#{__dirname}/index.html&quot;

app.all &quot;#{config.apiPath}*&quot;, (req, res) =&gt; 
  console.log &quot;REQUEST: &quot;, req.url, req.body || &quot;no body&quot;

  data = JSON.stringify(req.body)

  restOptions =
    username:  config.applicationID
    password:  config.masterKey
    data:      data
    method:    req.method.toLowerCase()
    headers:
      'Content-Type':  'application/json'
      'Content-Length': data?.length || 0

  complete = (data) =&gt; 
    console.log &quot;COMPLETE: &quot;, data
    res.json JSON.parse(data)

  error = (data, res) =&gt;
    console.log &quot;FAILURE: &quot;, data, res

  restler.request(&quot;https://api.parse.com/1/classes/#{req.url.replace(config.apiPath, '')}&quot;, restOptions)
    .on(&quot;complete&quot;, complete)
    .on(&quot;error&quot;, error)

app.get &quot;/*&quot;, (req, res) =&gt;
  res.sendfile &quot;#{__dirname}#{req.url}&quot;

app.listen config.port

console.log &quot;Server started on #{config.port}&quot;</pre>

This light-weight proxy will open a port and listen to requests.&#160; If you request “/” or any other file, it will dump it back.&#160; If you request “/data/*”, it will proxy your request off to Parse.com and return the result back.&#160; It will handle all of the GET/POST/PUT/DELETE requests that Parse.com supports.

This proxy server loads a config file:

**config.coffee**

<pre class="brush: coffeescript; ruler: true; toolbar: false; smart-tabs: false;">module.exports =  
  applicationID: 'Your Parse.com Application ID'
  masterKey:     'Your Parse.com Master Key'
  apiPath:  '/data/'
  port:     3001</pre>

The first two configuration options are provided to you by Parse.com when you create a new “App” with them.&#160; All of your entities for that “App” will have the same Application ID and Master Key.

Next is the path you want the proxy to use when passing data requests on.&#160; In this example, [http://mydomain.com/data/People](http://mydomain.com/data/People) would proxy off to [http://api.parse.com/1/classes/People](http://api.parse.com/1/classes/People).

Finally you can specify the port to run the proxy on.&#160; This is self explanatory.

Now all I can run my server: <font face="Courier New">coffee proxy.coffee</font>

Using curl, I can test all of the actions that my new server supports:

### Create

<font face="Courier New">&gt; curl -X POST -H &quot;Content-Type: application/json&quot; \ 

&gt; -d '{&quot;first&quot;: &quot;Brian&quot;, &quot;last&quot;: &quot;Genisio&quot;}' \</font>

  <p>&gt; 

  [<font face="Courier New">http://localhost:3001/data/People</font>](http://localhost:3001/data/People)<font face="Courier New"> 

{&quot;createdAt&quot;:&quot;2011-11-05T19:11:25.873Z&quot;,&quot;objectId&quot;:&quot;fiIMd53m0j&quot;}&#160; </font></p>

### List&#160; 

<font face="Courier New">&gt; curl -X GET </font>[<font face="Courier New">http://localhost:3001/data/People</font>](http://localhost:3001/data/People)<font face="Courier New"> 

{&quot;results&quot;:[{&quot;updatedAt&quot;:&quot;2011-11-05T19:11:25.873Z&quot;,&quot;last&quot;:&quot;Genisio&quot;,&quot;createdAt&quot;:&quot;2011-11-05T19:11:25.873Z&quot;,&quot;first&quot;:&quot;Brian&quot;,&quot;objectId&quot;:&quot;fiIMd53m0j&quot;}]}&#160; </font>

### Update&#160; 

<font face="Courier New">&gt; curl -X PUT -H &quot;Content-Type: application/json&quot; \&#160; 
&gt; -d '{&quot;middle&quot;: &quot;Michael&quot;}' \&#160; 
&gt; </font>[<font face="Courier New">http://localhost:3001/data/People/fiIMd53m0j</font>](http://localhost:3001/data/People/fiIMd53m0j)<font face="Courier New"> 

{&quot;updatedAt&quot;:&quot;2011-11-05T19:15:25.122Z&quot;}&#160; </font>

### Show&#160; 

<font face="Courier New">&gt; curl -X GET </font>[<font face="Courier New">http://localhost:3001/data/People/fiIMd53m0j</font>](http://localhost:3001/data/People/fiIMd53m0j)<font face="Courier New">&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; 
{&quot;middle&quot;:&quot;Michael&quot;,&quot;updatedAt&quot;:&quot;2011-11-05T19:15:25.122Z&quot;,&quot;last&quot;:&quot;Genisio&quot;,&quot;createdAt&quot;:&quot;2011-11-05T19:11:25.873Z&quot;,&quot;first&quot;:&quot;Brian&quot;,&quot;objectId&quot;:&quot;fiIMd53m0j&quot;}&#160;&#160; </font>

### Delete&#160; 

<font face="Courier New">&gt; curl -X DELETE </font>[<font face="Courier New">http://localhost:3001/data/People/fiIMd53m0j</font>](http://localhost:3001/data/People/fiIMd53m0j)<font face="Courier New"> 

{}</font>&#160;

### Index.html

<font face="Courier New">&gt; curl </font>[<font face="Courier New">http://localhost:3001</font>](http://localhost:3001)<font face="Courier New"> 

I AM AN HTML FILE</font>

### Other assets

<font face="Courier New">&gt; curl </font>[<font face="Courier New">http://localhost:3001/foo.bar</font>](http://localhost:3001/foo.bar)<font face="Courier New"> 

I AM A TEXT FILE</font>

Considering my entire server is less than 50 lines of code, I am happy with this compromise.&#160; My technology (the browser) does not allow me to go completely server-less but this gets me pretty close. 

In the next post, I will show how I got Backbone.js to talk to my proxy server.

[Making Backbone.js work with Parse.com &gt;&gt;](http://houseofbilz.com/archives/2011/11/07/making-backbone-js-work-with-parse-com/)