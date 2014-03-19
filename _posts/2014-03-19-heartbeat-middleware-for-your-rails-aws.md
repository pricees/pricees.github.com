---
layout: post
title: "Heartbeat Middleware for your Rails AWS"
description: ""
category: 
tags: []
---
{% include JB/setup %}


## The Problem ##

When migrating to a cloud service, I am going to want to set alerts that
check the health of my app. This response can very short, I will just return
a HEAD 200.

## The Solution ##

Create rack middleware that responds to an arbitrary url -- "/heartbeat" -- with a 200 HTTP (OK)
status code. 

Rack middleware is a breeze.
Here is an example using the stodgy style:

{% highlight ruby linenos %}
#
# lib/middleware/heartbeat.rb 
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
#
# lib/middleware/heartbeat.rb 
# hipster style uses a struct
#

class Heartbeat < Struct.new(:app)

  def call(env)
    if env["PATH_INFO"] == "/heartbeat"
      return [200, {}, []]
    end

    app.call(env)  # NOTE: call #app method not the ivar
  end
end
{% endhighlight %}

Lastly, add your middleware to the config. Open
"RAILS_ROOT/config/application.rb", find all the other config statements, and
insert your middle where.:

{% highlight ruby linenos %}
  ...

  config.middleware.use "Heartbeat"

  ...
{% endhighlight %}


## Conclusion ##

There you have it! A rack middleware handler that lets everyone know that your
app is being served. Now, go forth and scale!
