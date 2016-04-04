---
layout: post
title: "Calling Actions in Directives"
description: "Techniques for executing actions on directives"
category: Software
tags: [Angular, Directives]
permalink: software/2014/03/05/calling-actions-in-directives.html
---

The other day, a coleague asked me what the best way is to call an action on a directive.  We know that directives can call back to the parent scope, but with isolated scopes, you have no way to call into a directive to execute an action.  This type of thing is pretty rare in Angular.  Usually, our directives respond to changes in data or state which an be based off of the built-in binding capabilities.  Sometimes, however, we need to call actions on a directive.  A video player directive, for example, can benefit from actions such as `video.play()` and `video.pause()`.  

Some of the things to consider:

 * When you have multiple instances, how do you tell them all to act?
 * When you have multiple instances, how do you tell only one to act?
 * How would multiple copies inside of an ng-repeat work?
 * We might want to avoid tight coupling (do we always?)

So I thought about it more, and I have three possible answers to this question (there are more, but these feel most natural).  I am open to thoughts.  Is there a better way that I am missing?  I'm basing some of this off of what other widget frameworks do.  Take WinForms/WPF/Silverlight/Flex, for instance.  In those worlds, the controls (directives) expose methods.  The caller gives it a name `<widget name="foo">` and the framework creates variables for you to call: `foo.bar()`.  Convenient, but Angular doesn't give us this out of the box.  What are some approaches to come close to that?

### Setup
I am assuming a directive called "arm" which can be composed to create a "puppet".  A "puppeteer" wants to control the arms of the puppets.  Each arm has an action called `function wave(){ alert('wave'); }`  All of the code for these examples can be found [here](http://codepen.io/BrianGenisio/pen/ituKv).

### Events
The most obvious solution mkhere is to use events.  Events are nice because they are built in.  Events are also a bit ugly, however, because they broadcast.  We can mitigate this by namespacing our events.  The caller can call `$broadcast('arm:wave')` and the directive can hook that event: `$scope.$on('arm:wave', wave)`.

This works well except that in our current implementation, we can't send a "wave" event to a specific directive.  If we borrow from other frameworks, we can just give the arm an `id` like this: `<arm id="left"></arm>`.  Now, the puppeteer can call it: `$broadcast('arm: wave', 'left')`.  The directive's code gets a bit more complex but not too bad.  It starts by binding id `scope: { id: '@' }` and then the event handler does this

```javascript
$scope.$on('arm:wave', function(e, id) {
    if(id === undefined || $scope.id === id) {
        wave();
    }
});
```

This works well.  You can broadcast to all of them by ommiting the id, or you can call one of them by using the id.  How might you do this in an `ng-repeat`?

```html
<arm ng-repeat="arm in arms" id="arm-{{ '{{arm.id' }}}}"></arm>
```

Individual calls would become `$broadcast('arm:wave', 'arm-' + arm.id)`

### Commands
Borrowing from other frameworks, this example invokes the "command" pattern.  It lets the directive define some commands that the puppeteer can call.  The directive would define a binding: `scope: { commands: '=?' }`.  The directive would then set the commands object: `$scope.commands = { wave: wave }`.  

The puppeteer can now hook up the command: `<arm commands="left"></arm>` and use it: `left.wave();`

This approach is arguably cleaner, but it introduces a new concept that isn't native to Angular.  How does it scale to `ng-repeat`?

```html
<arm ng-repeat="arm in arms" commands="armControls[arm.i]">
```

Broadcast would become: 

```js
armControls.forEach(function(arm) { arm.wave(); });
```

Individual calls would become: `armControls[arm.id].wave()`

### Controller Expose
Ok, so **events** are a bit chatty and more code than we want to copy everytime.  **Commands** are less code and less chatty, but they aren't native.  It turns out that Angular already has a way to communicate via directives (directive-to-directive) by assigning functions to the controller itself.  Like this: `this.wave = wave;`.  But the puppeteer doesn't have access to the directive's controller.  

We can expose it!  Create a binding in the scope: `scope: { controller: '=?' }`.  Then, in the controller, you can just assign it: `$scope.controller = this;`.

Calling it is very similar to **commands**:

```html
<arm controller="left">
```

Access to the actions and `ng-repeat` is exactly the same as the **commands** option, but the way you wire it up is more Angular-like.  With **commands**, you expose only what you want, but you need to explicitly expose everything.  With **controller**, you only do it once, and you don't have to explicitly wire up the action, but you now get access to everything.

### Conclusion
In the end, I prefer the **events** mechanism if I am looking for a decoupled approach but a **controller** approach if I am looking for something a bit more coupled. I am soliciting feedback from the community since I know they are likely to have opinions.  Again, you can play with all three approaches [here](http://codepen.io/BrianGenisio/pen/ituKv).  Thoughts?
