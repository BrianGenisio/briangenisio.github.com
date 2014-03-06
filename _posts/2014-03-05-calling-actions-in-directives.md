---
layout: post
title: "Calling Actions in Directives"
description: "Techniques for executing actions on directives"
category: Software
tags: [Angular, Directives]
---
{% include JB/setup %}

This thread is meant to be a discussion.  A coleague asked me yesterday what the best way is to call an action on a directive.  We know that directives can call back to the parent scope, but with isolated scopes, you have no way to call into a directive.

I gave an example of how you might do it, but another coleague challenged me to think harder about it.  Some concerns he has are (paraphrased):
  * There are multiple ways to do it.  It feels like we should only have one
  * How does it work when you have multiple directives?
    - How do you tell them ALL to do it?
    - How do you tell only one of them to do it?
  * How would multiple copies inside of an ng-repeat work?
  * We want to avoid tight coupling (do we always?)

So I thought about it more, and I have three possible answers to this question.  I'm wondering what you all think.  I'm basing some of this off of what other widget frameworks do.  Take WPF/Silverlight/Flex, for instance.  In those worlds, the controls (directives) expose methods.  The caller gives it a name `<widget name="foo">` and the framework creates variables for you to call: `foo.bar()`.  Convenient, but Angular doesn't give us this.  What are some approaches to come close to that?

**Setup**
I am assuming a directive called "arm" which can be composed to create a puppet.  The puppeteer wants to control these arms.  Each arm has an action called `function wave(){ alert('wave'); }`  All of the code for these examples can be found [here](http://codepen.io/BrianGenisio/pen/ituKv).

**Events**
The most obvious solution here is to use events.  Events are nice because they are built in.  Events are a bit ugly, because they are broadcast.  We can fix this by namespacing our events.  The caller can call `$broadcast('arm:wave')` and the directive can hook that event: `$scope.$on('arm:wave', wave)`.

This works well, except we can't send a "wave" event to a specific directive.  If we borrow from other frameworks, we can just give the arm an `id` like this: `<arm id="left">`.  Now, the puppeteer can call it: `$broadcast('arm: wave', 'left')`.  The directive's code gets a bit more complex but not too bad.  It starts by binding id `scope: { id: '@' }` and then the event handler does this

{% highlight javascript %}
$scope.$on('arm:wave', function(e, id) {
    if(id === undefined || $scope.id === id) {
        wave();
    }
});
{% endhighlight %}

This works well.  You can broadcast to all of them by ommiting the id, or you can call one of them by using the id.  How might you do this in an `ng-repeat`?

{% highlight html %}
<arm ng-repeat="arm in arms" id="arm-{{ '{{arm.id' }}}}">
{% endhighlight %}

Individual calls would become `$broadcast('arm:wave', 'arm-' + arm.id)`

**Commands**
Borrowing from other frameworks, this example invokes the "command" pattern.  It lets the directive define some commands that the puppeteer can call.  The directive would define a binding: `scope: { commands: '=?' }`.  The directive would then set the commands object: `$scope.commands = { wave: wave }`.  

The puppeteer can now hook up the command: `<arm commands="left">` and use it: `left.wave();`

This approach is arguably cleaner, but it introduces a new concept that isn't native to Angular.  How does it scale to `ng-repeat`?

{% highlight html %}
<arm ng-repeat="arm in arms" commands="armControls[arm.i]">
{% endhighlight %}

Broadcast would become: 

{% highlight javascript %}
armControls.forEach(function(arm) { arm.wave(); });
{% endhighlight %}

Individual calls would become: `armControls[arm.id].wave()`

**Controller Expose**
Ok, so **events** are a bit chatty and more code.  **commands** are less code and less chatty, but they aren't native.  We already have a way to communicate via directives (directive-to-directive) by assigning functions to the controller itself.  Like this: `this.wave = wave;`.  But the puppeteer doesn't have access to the directives controller.  

We can expose it!  Create a binding in the scope: `scope: { controller: '=?' }`.  Then, in the controller, you can just assign it: `$scope.controller = this;`.

Calling it is very similar to **commands**:

{% highlight html %}
<arm controller="left">
{% endhighlight %}

Access to the actions and `ng-repeat` is exactly the same as the **commands** option, but the way you wire it up is more Angular-like.  With **commands**, you expose only what you want, but you need to explicitly expose everything.  With **controller**, you only do it once, and you don't have to explicitly wire up the action, but you now get access to everything.  

**Conclusion**
So there it is.  A more thought-out write-up of the concept.  I am soliciting feedback from the community since I know they are likely to have opinions.  Again, you can play with all three approaches [here](http://codepen.io/BrianGenisio/pen/ituKv).
