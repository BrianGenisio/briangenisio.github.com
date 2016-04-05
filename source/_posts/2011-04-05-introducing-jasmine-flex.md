---
title: Introducing Jasmine-Flex
date: 2011-04-05 11:14:43
layout: post
category: Software
tags: [BDD, Flex, jasmine, testing]
permalink: archives/2011/04/05/introducing-jasmine-flex/
---

I have been developing software using Test Driven Development ([TDD](http://en.wikipedia.org/wiki/Test_driven_development)) for over three years now.  I usually use traditional xUnit style testing frameworks ([NUnit](http://www.nunit.org/), [JUnit](http://www.junit.org/), [jsUnit](http://www.jsunit.net/), [FlexUnit](http://www.flexunit.org/), [cppUnit](http://sourceforge.net/apps/mediawiki/cppunit/index.php?title=Main_Page "Main_Page"), etc) because they are the most accessible frameworks out there.

In reality, though, my TDD style has evolved into more of a Behavior Driven Development ([BDD](http://en.wikipedia.org/wiki/Behavior_driven_development)) style.  I specify how my software behaves opposed to writing tests against the inputs/outputs of a given function.  Although this can be a bit awkward with the xUnit style of testing, it can be done ANDI have done so successfully for quite some time.

While I am doing this, I always think to the times when I develop in Ruby.  When in Ruby, I don’t use the standard xUnit tool.  Instead, I use [RSpec](http://rspec.info/).  RSpec allows me to organize my tests (specifications) more organically and within context.  The result is a more expressive, better organized test suite more suited for BDD-style development.

Lately, however, I have been spending a lot of time with the Flex framework.  I wanted to find a BDD context specification tool for that environment but I came up empty.  I got excited when I found one in JavaScript, and it gave me hope.

### Enter Jasmine

[Jasmine](http://pivotal.github.com/jasmine/) is a BDD testing tool written in JavaScript and used in all sorts of environments including [Ruby on Rails](http://rubyonrails.org/) and [Node.js](http://nodejs.org/).  I put my feet to the fire and hacked out a port of this code base to ActionScript.  I am pleased to announce that [Jasmine-Flex](https://github.com/BrianGenisio/jasmine-flex/wiki) is ready to go!

Using Jasmine-Flex is simple:

1.  Create a new Flex project (Flash or Air)
2.  Download JasmineFlex.swc and put it in your libs folder.
3.  Put the **TrivialFlexReporterView** in your application and set **autoExecute** to true.
4.  Add your specs using the **script** tag
5.  Run the app.
Here is a step-by-step example:

1\. Create a new Flex project (Flash or Air)

[![step1](http://houseofbilz.com/wp-content/uploads/2011/04/step1_thumb.png "step1")](http://houseofbilz.com/wp-content/uploads/2011/04/step1.png)

2\. Download JasmineFlex.swc and put it in your libs folder

[![step2](http://houseofbilz.com/wp-content/uploads/2011/04/step2_thumb.png "step2")](http://houseofbilz.com/wp-content/uploads/2011/04/step2.png)

3\. Put the **TrivialFlexReporterView** in your application and set **autoExecute** to true.

[![step3](http://houseofbilz.com/wp-content/uploads/2011/04/step3_thumb.png "step3")](http://houseofbilz.com/wp-content/uploads/2011/04/step3.png)

4\. Add your specs using the **script** tag

[![step4a](http://houseofbilz.com/wp-content/uploads/2011/04/step4a_thumb.png "step4a")](http://houseofbilz.com/wp-content/uploads/2011/04/step4a.png)

[![step4b](http://houseofbilz.com/wp-content/uploads/2011/04/step4b_thumb.png "step4b")](http://houseofbilz.com/wp-content/uploads/2011/04/step4b.png)

5\. Run the app

[![step5](http://houseofbilz.com/wp-content/uploads/2011/04/step5_thumb.png "step5")](http://houseofbilz.com/wp-content/uploads/2011/04/step5.png)

### You are up and running

Now that you have a Jasmine testing environment up and running, you continue to use Jasmine to write specs, watch them fail, and make them pass.  Add as many spec suites as you need using the **script** tag (step 3).  Before you know it, you will have a rich suite of executable specifications!

### Need more information?

*   See the documentation on the [Jasmine-Flex](https://github.com/BrianGenisio/jasmine-flex/wiki) site.
*   Take a look at an [example](https://github.com/BrianGenisio/jasmine-flex/tree/master/flex/example) project.
*   See the specs for the [Jasmine](https://github.com/BrianGenisio/jasmine-flex/tree/master/flex/specs/src/suites) project (Jasmine was built using Jasmine!).
*   Check out [The RSpec Book](http://www.pragprog.com/titles/achbd/the-rspec-book). Most of the concepts apply directly to Jasmine.