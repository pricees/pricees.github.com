---
layout: post
title: "Health Check Middleware for your Rails AWS"
description: ""
category: 
tags: []
---
{% include JB/setup %}


### The Problem ###

When migrating to a cloud service, I am going to want to set alerts that
check the health of my app. This response can very short, I will just return
a HEAD 200.

### The Solution ###

Create rack middleware that responds to an arbitrary url -- "/health_check" -- with a 200 HTTP (OK)
status code. 

Rack middleware is a breeze.
Here is an example using the stodgy style:

{% highlight ruby  %}
#
# lib/middleware/health_check.rb 
# 
class HealthCheck 

  def initialize(app)
    @app = app
  end

  def call(env)
    if env["PATH_INFO"] == "/health_check"
      return [200, {}, []]
    end

    @app.call(env)
  end
end
{% endhighlight %}

I prefer the hipster-style, using a struct:

{% highlight ruby  %}
#
# lib/middleware/health_check.rb 
# hipster style uses a struct
#

class HealthCheck < Struct.new(:app)

  def call(env)
    if env["PATH_INFO"] == "/health_check"
      return [200, {}, []]
    end

    app.call(env)  # NOTE: call #app method not the ivar
  end
end
{% endhighlight %}

Lastly, add your middleware to the config. Open
"RAILS_ROOT/config/application.rb", find all the other config statements, and
insert your middle where.:

{% highlight ruby  %}
  ...

  config.middleware.use "HealthCheck"

  ...
{% endhighlight %}

### Got SSL? ###

If you are enforcing SSL connections in your Rails config you will want to
exclude this path, a-like so:

{% highlight ruby  %}
# config/environmnents/production.rb

  ...
config.force_ssl = true
config.ssl_options = { exclude: proc { |env| env['PATH_INFO'].start_with?('/health_check') } }
  ...
{% endhighlight %}

### Conclusion ###

There you have it! A rack middleware handler that lets everyone know that your
app is being served. Now, go forth and scale!
