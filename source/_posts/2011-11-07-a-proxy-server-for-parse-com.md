---
title: A Proxy Server for Parse.com
date: 2011-11-07 11:41:46
layout: post
category: Software
tags: [JavaScript, CoffeeScript, Heroku, Development, HTML5, Backbone, Node.js, Parse.com, Technology, Web]
permalink: archives/2011/11/07/a-proxy-server-for-parse-com/

---

[&lt;&lt; See the full series of posts](/archives/2011/11/07/going-mostly-server-less-with-backbone-js/)&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160;&#160; [Making Backbone.js work with Parse.com &gt;&gt;](/archives/2011/11/07/making-backbone-js-work-with-parse-com/)

In my [previous post](/archives/2011/11/07/going-mostly-server-less-with-backbone-js/), I asked the question: “Is there any reason why can’t we use a service like [Parse.com](https://www.parse.com/) in an HTML5/Javascript single-paged app?”&#160; The answer is “yes”.

The reason: [the same origin policy](http://en.wikipedia.org/wiki/Same_origin_policy).&#160; The browser won’t let you request Parse.com data from a different domain.&#160; There are certainly tricks you can play if you just want to GET the data (like JSONP for example), but there is no good way to make POST/PUT/DELETE requests.&#160; You have to stay on your own domain if you want to use all that a REST API has to offer.

Using my newfound [CoffeeScript](http://jashkenas.github.com/coffee-script/) skills, I came up with this proxy server based on [Node.js](http://nodejs.org/) with [Express](http://expressjs.com/) and [Restler](https://github.com/danwrong/restler).&#160; While I’m at it, the server can return my index.html and all other assets (images, Javascript, CSS and HTML templates). 

Here is the code (full code for this post is available on GitHub [https://github.com/BrianGenisio/Parseback/tree/parse.com-proxy-with-readme](https://github.com/BrianGenisio/Parseback/tree/parse.com-proxy-with-readme)):

**proxy.coffee**
```coffee
express = require ‘express’
restler = require ‘restler’
config = require ‘./config’

app = express.createServer()
app.use express.bodyParser()

app.get "/", (req, res) =>
  res.sendfile "#{__dirname}/index.html"

app.all "#{config.apiPath}*", (req, res) => 
  console.log "REQUEST: ", req.url, req.body || "no body"

  data = JSON.stringify(req.body)

  restOptions =
    username:  config.applicationID
    password:  config.masterKey
    data:      data
    method:    req.method.toLowerCase()
    headers:
      'Content-Type':  'application/json'
      'Content-Length': data?.length || 0

  complete = (data) => 
    console.log "COMPLETE: ", data
    res.json JSON.parse(data)

  error = (data, res) =>
    console.log "FAILURE: ", data, res

  restler.request("https://api.parse.com/1/classes/#{req.url.replace(config.apiPath, '')}", restOptions)
    .on("complete", complete)
    .on("error", error)

app.get "/*", (req, res) =>
  res.sendfile "#{__dirname}#{req.url}"

app.listen config.port

console.log "Server started on #{config.port}"
```

  

This light-weight proxy will open a port and listen to requests.&#160; If you request “/” or any other file, it will dump it back.&#160; If you request “/data/*”, it will proxy your request off to Parse.com and return the result back.&#160; It will handle all of the GET/POST/PUT/DELETE requests that Parse.com supports.

This proxy server loads a config file:

**config.coffee**
```coffee
module.exports =  
  applicationID: 'Your Parse.com Application ID'
  masterKey:     'Your Parse.com Master Key'
  apiPath:  '/data/'
  port:     3001
```

The first two configuration options are provided to you by Parse.com when you create a new “App” with them.&#160; All of your entities for that “App” will have the same Application ID and Master Key.

Next is the path you want the proxy to use when passing data requests on.&#160; In this example, [http://mydomain.com/data/People](http://mydomain.com/data/People) would proxy off to [http://api.parse.com/1/classes/People](http://api.parse.com/1/classes/People).

Finally you can specify the port to run the proxy on.&#160; This is self explanatory.

Now all I can run my server: `coffee proxy.coffee`

Using curl, I can test all of the actions that my new server supports:

### Create
```bash
> curl -X POST -H "Content-Type: application/json" \
>  -d ‘{"first": "Brian", "last": "Genisio"}’ \
>  http://localhost:3001/data/People

  {"createdAt":"2011-11-05T19:11:25.873Z","objectId":"fiIMd53m0j"} 
```

### List
```bash
> curl -X GET http://localhost:3001/data/People

  {"results":[{"updatedAt":"2011-11-05T19:11:25.873Z","last":"Genisio","createdAt":"2011-11-05T19:11:25.873Z","first":"Brian","objectId":"fiIMd53m0j"}]} 
```

### Update
```bash
> curl -X PUT -H "Content-Type: application/json" \ 
>  -d ‘{"middle": "Michael"}’ \ 
>  http://localhost:3001/data/People/fiIMd53m0j

  {"updatedAt":"2011-11-05T19:15:25.122Z"} 
```

### Show
```bash
> curl -X GET http://localhost:3001/data/People/fiIMd53m0j                         
  {"middle":"Michael","updatedAt":"2011-11-05T19:15:25.122Z","last":"Genisio","createdAt":"2011-11-05T19:11:25.873Z","first":"Brian","objectId":"fiIMd53m0j"}  
```

### Delete
```bash
> curl -X DELETE http://localhost:3001/data/People/fiIMd53m0j

  {} 
```

### Index.html
```bash
> curl http://localhost:3001

  I AM AN HTML FILE
```

### Other assets
```bash
> curl http://localhost:3001/foo.bar

  I AM A TEXT FILE
```

Considering my entire server is less than 50 lines of code, I am happy with this compromise.&#160; My technology (the browser) does not allow me to go completely server-less but this gets me pretty close. 

In the next post, I will show how I got Backbone.js to talk to my proxy server.

[Making Backbone.js work with Parse.com &gt;&gt;](/archives/2011/11/07/making-backbone-js-work-with-parse-com/)