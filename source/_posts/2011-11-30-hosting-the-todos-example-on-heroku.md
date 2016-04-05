---
title: Hosting the Todos example on Heroku
date: 2011-11-30 20:40:06
layout: post
category: Software
tags: [JavaScript, Heroku, Backbone, Node]
permalink: archives/2011/11/30/hosting-the-todos-example-on-heroku/
---

[Series Overview](/archives/2011/11/07/going-mostly-server-less-with-backbone-js/)

[&lt;&lt; An Example Backbone/Parse.com App](/archives/2011/11/30/an-example-backboneparse-com-app/)

The Todos example was extremely easy to get up and running with Heroku.&#160; Even though this is a CoffeeScript based server running with Node.js, Heroku can support the application.&#160; I won’t go over the install process for the pre-requisites (Git and Heroku) because it is easy to [find on the web](http://www.jamesward.com/2011/06/21/getting-started-with-node-js-on-the-cloud/).&#160; Assuming they are installed, deploy the app like this:

### Pull down the Todos example

```bash
> git clone git@github.com:BrianGenisio/todos-parseback.git
```

### Create the Heroku app

```bash
> heroku create -s cedar yourappname          
    Creating yourappname... done, stack is cedar http://yourappname.herokuapp.com/ | git@heroku.com:yourappname.git      
    Git remote heroku added</font>
```

### Create file: package.json

```json
{
  "name": "yourappname",
  "version": "0.0.1",
  "dependencies": {
    "express": "2.5.0",
    "coffee-script": "1.1.3",
    "restler": "0.2.1"
  }
}
```

### Install the packages locally (for testing)

```bash
> npm install
```

### Configure and test the app

```bash
> cp config.coffee.example config.coffee
```

Edit **config.coffee** per the steps in the previous article and run it:

```bash
> coffee proxy.coffee
```

Point your browser to [http://localhost:3001](http://localhost:3001) and play.&#160; Everything should be working right now locally.&#160; Next, lets get it up on Heroku.

### Create file: run.js

```javascript
# Necessary because Heroku can’t run CoffeeScript directly
require("coffee-script");
require("./proxy.coffee");
```

### Create file: Procfile

<font face="Courier New">web: node run.js</font>

### Deploy to Heroku (via git)
```bash
> git add .
> git commit -m "Adding Heroku files"

  ** [master 0154577] Adding Heroku files
  3 files changed, 12 insertions(+), 0 deletions(-)
  create mode 100644 Procfile
  create mode 100644 package.json
  create mode 100644 run.js

> git add .
> git commit -m "Adding Heroku files"

  ** [master 0154577] Adding Heroku files
  3 files changed, 12 insertions(+), 0 deletions(-)
  create mode 100644 Procfile
  create mode 100644 package.json
  create mode 100644 run.js

> git push heroku master

  Counting objects: 23, done.
  Delta compression using up to 4 threads.
  Compressing objects: 100% (21/21), done.
  Writing objects: 100% (23/23), 102.20 KiB, done.
  Total 23 (delta 1), reused 17 (delta 0) —–> Heroku receiving push
  —–> Node.js app detected
  —–> Fetching Node.js binaries
  —–> Vendoring node 0.4.7
  —–> Installing dependencies with npm 1.0.94
       restler@0.2.1 ./node_modules/restler
       coffee-script@1.1.3 ./node_modules/coffee-script
       express@2.5.0 ./node_modules/express
       ├── mkdirp@0.0.7
       ├── mime@1.2.4
       ├── qs@0.3.2
       └── connect@1.7.3
       Dependencies installed
  —–> Discovering process types
       Procfile declares types -> web
  —–> Compiled slug size is 3.4MB
  —–> Launching… done, v4
    http://yourappname.herokuapp.com deployed to Heroku To git@heroku.com:yourappname.git
  * [new branch]      master –> master
```

### Try it out

```bash
> heroku open
  Opening http://yourappname.herokuapp.com/
```

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/f0dd85a2-2d24-43c6-bb73-1319e835105a/2011-11-30_14-22-43.png)

### That’s it!

Seriously.&#160; That was easy.&#160; Now the app is hosted.&#160; Heroku and Parse.com do all the dirty work for you.

<font color="#333333" face="Courier New">

</font>