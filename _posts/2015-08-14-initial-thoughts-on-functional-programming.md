---
layout: post
title: "Initial thoughts on functional programming"
description: ""
category: 
tags: []
---
{% include JB/setup %}

According to this blog I read, my blog entries don't need to be long tomes for the world to read, but simply tid bits of my thoughts. I like this better.
I have spent 7 months waiting to have the time to combose the blog entry of the year for my readership of zero. Wasted time.

I start my foray into "more than micro blogging, less than long form blogging" with my initial thoughts on functional programming.

### Why am I doing this ###

I have a decent grasp of imperative and object oriented programming styles.
I wanted to dive into functional because our data team using Clojure. Some of our web team is looking into React. And I haven't really considered any of it.

### Time, State, Identity and Utopia ###

I love the idea that __time__ is a fluid concept, a stream from a source point of some entity to an end point. The __identity__ of this entity is all of the mutations that this entity goes through, if any, during some arbitrary length of __time__. __State__ it simply a snapshot of the identify in time. It is immutable, that is: it doesn't change.

Keeping track of state in object-oriented programming is the pervasive bugbear that has been the source of waited dev-hours.

Functional programming, by separating these discrete concepts, does us a world of good in removing bugs caused by code that erroneously causes side effects.

I love the idea of pure functions, just like in mathematics. I give input and I am sure to give output. No side effects; __state__ is passed in.

This type of idempotency keeps the code rather manageable.

### Locals ###

There are no local variables, but there are locals that values are bound.
This is one of the factors that make Erlang elegant. You bind a value to a symbol and you don't change it. It is what it is, pure Aristatelian identity.

When scoped to a pure function, there is no clean up.

### Currying and Monadic Functions ###

The only thing less complex than a monad, in my humble opinion, is a function that accepts no params. However, most non-trivial applications will need inputs. 
Using partial evaluation of functions and currying polyadic functions into monodic, simplifies understand what is occuring in the execution path.

### Less dots, more composition ###

I love a good ole fashion method chain that spans 80 characters and starts flame wars on stackoverflow between "trailing period" or "leading period." Code execution as a stream of inputs and outputs of data as a snapshot in time, illuminates the changes and makes the most complex operations understandable.

### Less State, More Happiness ###
I would be remiss if I didn't inject some libertarian idealism.
By removing mutable state, my gut instinct is that programs build less cruft, mand keep the code cleaner.

### Why not functionalize all the things ###

Most applications that I write will still be object-oriented, its the way that we model complex problems in our industry. Having said that, I will look for areas where functional programming strengths shine, analysis of large streams of data, for example.

These are just my thoughts. After I finish read "The Joy of Closure" I may return to find that all of these initial thoughts are incorrect. I call that "The Joy of Remaining Teachable" in an ever, and rapidly, changing occupation like software engineering.


