---
title: Hosting the Todos example on Heroku
id: 407
categories:
  - Uncategorized
date: 2011-11-30 20:40:06
tags:
---

[Series Overview](http://houseofbilz.com/archives/2011/11/07/going-mostly-server-less-with-backbone-js/)

[&lt;&lt; An Example Backbone/Parse.com App](http://houseofbilz.com/archives/2011/11/30/an-example-backboneparse-com-app/)

The Todos example was extremely easy to get up and running with Heroku.&#160; Even though this is a CoffeeScript based server running with Node.js, Heroku can support the application.&#160; I won’t go over the install process for the pre-requisites (Git and Heroku) because it is easy to [find on the web](http://www.jamesward.com/2011/06/21/getting-started-with-node-js-on-the-cloud/).&#160; Assuming they are installed, deploy the app like this:

### Pull down the Todos example

<font size="3" face="Courier New">**&gt; git clone git@github.com:BrianGenisio/todos-parseback.git**</font>

### Create the Heroku app

<font face="Courier New"><font size="3">**&gt; heroku create -s cedar yourappname          
**</font><font color="#333333">Creating yourappname... done, stack is cedar        
</font></font><font color="#333333" face="Courier New">http://yourappname.herokuapp.com/</font><font color="#333333" face="Courier New"> | git@heroku.com:yourappname.git      
Git remote heroku added</font>

### Create file: package.json

  <pre class="brush: js; ruler: true; toolbar: false; smart-tabs: false;">{
  &quot;name&quot;: &quot;yourappname&quot;,
  &quot;version&quot;: &quot;0.0.1&quot;,
  &quot;dependencies&quot;: {
    &quot;express&quot;: &quot;2.5.0&quot;,
    &quot;coffee-script&quot;: &quot;1.1.3&quot;,
    &quot;restler&quot;: &quot;0.2.1&quot;
  }
}</pre>

### Install the packages locally (for testing)

<font size="3" face="Courier New">**&gt; npm install**</font>

### Configure and test the app

<font size="3" face="Courier New">**&gt; cp config.coffee.example config.coffee**</font>

Edit **config.coffee** per the steps in the previous article and run it:

<font size="3">**&gt; coffee proxy.coffee**</font>

Point your browser to [http://localhost:3001](http://localhost:3001) and play.&#160; Everything should be working right now locally.&#160; Next, lets get it up on Heroku.

### Create file: run.js

<pre class="brush: coffeescript; ruler: true; toolbar: false; smart-tabs: false;"># Necessary because Heroku can’t run CoffeeScript directly
require(&quot;coffee-script&quot;);
require(&quot;./proxy.coffee&quot;);</pre>

### Create file: Procfile

<font face="Courier New">web: node run.js</font>

### Deploy to Heroku (via git)

<font face="Courier New">**<font size="3">&gt; git add .

&gt; git commit -m &quot;Adding Heroku files&quot;

</font>**<font color="#333333" size="2">[master 0154577] Adding Heroku files

 3 files changed, 12 insertions(+), 0 deletions(-)

 create mode 100644 Procfile

 create mode 100644 package.json

 create mode 100644 run.js</font></font>

<font face="Courier New">**<font size="3">&gt; git push heroku master

</font>**<font color="#333333">Counting objects: 23, done.

Delta compression using up to 4 threads.

Compressing objects: 100% (21/21), done.

Writing objects: 100% (23/23), 102.20 KiB, done.

Total 23 (delta 1), reused 17 (delta 0)</font></font>

<font color="#333333" face="Courier New">-----&gt; Heroku receiving push

-----&gt; Node.js app detected

-----&gt; Fetching Node.js binaries

-----&gt; Vendoring node 0.4.7

-----&gt; Installing dependencies with npm 1.0.94

&#160;&#160;&#160;&#160;&#160;&#160; restler@0.2.1 ./node_modules/restler 

&#160;&#160;&#160;&#160;&#160;&#160; coffee-script@1.1.3 ./node_modules/coffee-script 

&#160;&#160;&#160;&#160;&#160;&#160; express@2.5.0 ./node_modules/express 

&#160;&#160;&#160;&#160;&#160;&#160; ├── mkdirp@0.0.7

&#160;&#160;&#160;&#160;&#160;&#160; ├── mime@1.2.4

&#160;&#160;&#160;&#160;&#160;&#160; ├── qs@0.3.2

&#160;&#160;&#160;&#160;&#160;&#160; └── connect@1.7.3

&#160;&#160;&#160;&#160;&#160;&#160; Dependencies installed

-----&gt; Discovering process types

&#160;&#160;&#160;&#160;&#160;&#160; Procfile declares types -&gt; web

-----&gt; Compiled slug size is 3.4MB

-----&gt; Launching... done, v4

&#160;&#160;&#160;&#160;&#160;&#160; </font>[<font color="#333333" face="Courier New">http://yourappname.herokuapp.com</font>](http://yourappname.herokuapp.com)<font color="#333333" face="Courier New"> deployed to Heroku</font>

<font color="#333333" face="Courier New">To git@heroku.com:yourappname.git

 * [new branch]&#160;&#160;&#160;&#160;&#160; master –&gt; master</font>

### Try it out

<font face="Courier New"><font size="3">**&gt; heroku open

**</font><font color="#333333">Opening </font></font>[<font color="#333333" face="Courier New">http://yourappname.herokuapp.com/</font>](http://yourappname.herokuapp.com/)

![](http://content.screencast.com/users/BrianGenisio/folders/Snagit/media/f0dd85a2-2d24-43c6-bb73-1319e835105a/2011-11-30_14-22-43.png)

### That’s it!

Seriously.&#160; That was easy.&#160; Now the app is hosted.&#160; Heroku and Parse.com do all the dirty work for you.

<font color="#333333" face="Courier New">

</font>