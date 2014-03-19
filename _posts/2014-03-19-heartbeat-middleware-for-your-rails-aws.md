---
layout: post
title: "Heartbeat Middleware for your Rails AWS"
description: ""
category: 
tags: []
---
{% include JB/setup %}


## The Problem ##

When migrating to a cloud service, you are going to want to set alerts that
check the health of your app. Some This response can very short, just return
a HEAD 200.


## The Solution ##

Create rack middleware that responds to "/heartbeat" and returns a 200 HTTP (OK)
status code. 

This is the stodgy style, with an initializer:

{% highlight rubylinenos %}
# lib/middleware/heartbreat.rb 
# 
class Heartbeat 

  def initialize(app)
    @app = app
  end

  def call(env)
    if env["PATH_INFO"] == "/heartbeat"
      return [200, {}, []]
    end

    @app.call(env)
  end
end
{% endhighlight %}

I prefer the hipster-style, using a struct:

{% highlight ruby linenos %}
# lib/middleware/heartbreat.rb 
# Hipster style with a struct

class Heartbeat < Struct.new(:app)

  def call(env)
    if env["PATH_INFO"] == "/heartbeat"
      return [200, {}, []]
    end

    app.call(env)  # NOTE: call #app method not the ivar
  end
end
{% endhighlight %}

Lastly, add your middleware to the config, open "ROOT/config/application.rb" :

{% highlight ruby linenos %}
  ...

  config.middleware.use "Heartbeat"

  ...
{% endhighlight %}


## Conclusion ##

There you have it. A lightweight handler that tells an outside service if
everything is a-OK. Now, go forth and scale!
