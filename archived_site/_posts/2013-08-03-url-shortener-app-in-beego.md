---
layout: post
title: "Url Shortener App in Beego"
description: "My first app in the Beego Framework"
category: golang
tags: [golang, mvc, web, beego]
---
{% include JB/setup %}

## The Problem ##

Look, I am going to level with you.  When I start learning a general purpose
language in the shell, the first thing I do is look at how to create a "Hello
World" program.  When I start learning a big, fat,
every-thing-but-the-kitchen-sink web framework, I look at how to create a
blogging app.  When I hear that a web framework is thin, and "lightweight" I
look at how to write a url shortener.  These are the starting points when I am
learning how to write in new languages.  So when I heard about this new playa on
tha block, Beego, and when I heard him calling himself "Sinatra-inspired," I was
like, "Oh snap! I gotsta write how to create a url shortener in this thang."

So here we go!

## The Solution ##

### Create a new application ###

First I opened my favorite shell and used the Bee tools package
{% highlight bash %}
$ bee new beego_url
{% endhighlight %}

This creates a nice directory tree and starter files that you can read about in
the beautiful documentation they provide.

{% highlight bash %}
$ go build main.go && ./main
{% endhighlight %}

Navigate to <code>http://localhost:8080</code>

### Just Bootstrap it! ###

You should see something pretty!
Now, I am no fan of design, because I believe I am design-non-inclined, so I
added in some boostrap. 

To test the new app build it (you apparently can't run it :/)

###  Lets get down to the muthascratchin code! ###

## Final Thoughts ##


