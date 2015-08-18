---
layout: post
title: "Compose and Curry: My two besties"
description: ""
category: 
tags: []
---
{% include JB/setup %}

# THIS POST IS A WORK IN PROGRESS #
As I continue my dive into functional programming in earnest, I am finding more and more things that I really enjoy about the paradigm.

Two of my favorites so far a composition and currying.

Composition is the method by which I can pass an arbitrary number of functions

{% highlight javascript linenos %}
_ = require('./ramda.min.js');

// Compose
var uppercase = function(str) { return str.toUpperCase(); }
var scream = function(str) { return str + "!!!"; }
var hello = function(str) { return "Hello, " + str; }
var momSMS = _.compose(hello, scream, uppercase);
console.log(momSMS("Ted"));
{% endhighlight %}

{% highlight bash %}
$ node compose_example.js
=> Hello, TED!!!

{% endhighlight %}
{% highlight javascript linenos %}
// Curry
var addN = function(n, m) { return n + m };
var curryAddN = _.curry(addN)
var add10 = curryAddN(10)
var add100 = curryAddN(100)
console.log(add10(15));
console.log(add100(15));
{% endhighlight %}
{% highlight bash %}
$ node curry_example.js
=> 25
115
{% endhighlight %}
