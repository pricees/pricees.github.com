---
layout: post
title: "AWS ELB, Nginx, Rails, SSL: The final word."
description: "How to set up SSL on ELB with an Nginx Rails stack"
category: 
tags: [aws, elb, nginx, rails, force_ssl, ssl, certs, termination]
---
{% include JB/setup %}

### The Problem ###

It goes without saying that moving to an AWS comes with many a hard faught
battle. Hack and foist, hack and slash, hackety hack. Today was all that and a
bunch more. I had deploys out the wazoo and nothing was working. Alas, I was
successful in my mission.

#### Nginx as an SSL Termination Proxy ####

The stack I had originally used, configured Nginx as the
[SSL termination proxy](http://en.wikipedia.org/wiki/SSL_termination_proxy). 
We created self signed certificates and the like.
We create the nginx config form that looked like so:

{% highlight bash %}

# HTTPS server

server {
  listen 443;
  server_name example.com;

  root /usr/share/nginx/www;
  index index.html index.htm;

  ssl on;
  ssl_certificate /etc/nginx/ssl/server.crt;
  ssl_certificate_key /etc/nginx/ssl/server.key; 
}
{% endhighlight %} 

Our Rails app had the production environment set "force_ssl" 
{% highlight bash %}
# config/environments/production.rb
... 

  # Force all access to the app over SSL, use Strict-Transport-Security, and use
  # secure cookies.
  config.force_ssl = true

  ...
{% endhighlight %} 

So when using a proper, or self-signed, certificate nginx works as I would
expect. When I attempt to access the app using <code>http://example.net</code>
I recieve an error. Since my entire app is on SSL lockdown, I may do the end
user a favor and redirect by adding this block to the nginx config:

{% highlight bash %}

# Redirect http -> https

server {
  listen 80;

  location / {
    return 301 https://$http_host$request_uri;
  }
}

# HTTPS server

...
{% endhighlight %} 

Then, when I request <code>http://example/net</code>I am redirected to <code>https://example.net</code>.

So we are good right?  Well, yes, for a simple server. But lets say you want to
throw this mutha on AWS. Well, son, you got a whole heap of trouble ahead of
you, unless you follow these guideslines. Goodness!

### AWS, AHOY! ###

__This assumes you want our rails app to be 100% secure. If you only want a
couple of controllers to be secure, please adjust our nginx confs and
production environment configs, accordingly.__

#### A little about that, there, ELB ####

Elastic LoadBalancer is our new shawty, our new beau. Its everything you
wanted and more. You can, if done correctly, bring up 1 instance or 1 million.
Another cool think about ELB is that it will act as our SSL termination proxy.
Lastly, you can forward requests to our instances, on ports that are different
than the ports they request came in on. We make use of this by taking a request
from port 443 and sending it to our instances over port 80.
Pretty sweet eh? 
All you need are a few tweaks to our configs and you are good to go.
Ready? Rock on!

#### Health Check for that Rails App ####

The first thing we need to do is get our rails app ready for a ELB. ELB wants
to know the status of our app, and it does this through a "health check."  Now,
when ELB doesn't recieve a HTTP 200 OK back from the health check, it
proactively moves that instance out of the rotation. This is great. 

The default health check pings <code>http://host/</code> every 4 seconds or so.
You can set it to be any protocol, port, path, etc. You can edit this value by
click on a load balance and navigating to "Health Check" on the bottom menu.

So if you are enterprising and would like to create a light weight health check
on port 80 (http), check out this post [Health Check Bonanza]({% post_url 2014-03-19-healthcheck-middleware-for-your-rails-aws %}). 
Otherwise, set the health check to be for protocol <code>HTTPS</code>, port
<code>443</code>, and leave the path <code>"/"</code>.
#### Can I get a witness? Nginx SSL ####

Now that we are no longer using Nginx as our termination proxy, we can listen
for input on a single port: 80. What we will be doing, however is redirect any
  requests that are made from http, and redirecting them to https.


{% highlight bash %}

upstream unicorn {
  server localhost:3000;
}

server {
  listen 80;
  server_name default_server;

  root        /var/www/example.net/current;

  index index.html index.htm;

  location /health_check {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_next_upstream error;
    proxy_pass http://unicorn;
    break;
  }

  location / {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_next_upstream error;

    if ($http_x_forwarded_proto != "https") {
      rewrite ^(.*)$ https://$host$1 permanent;
    }

    proxy_pass http://unicorn;

    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains;";
  }
}
{% endhighlight %} 

So this is a lot to chew on. Hopefully, the upstream block looks familiar.
Lets to a look at the other blocks. 

I use *catch-all server_name* <code>default_server</code>. We are loading one server
onto one instance with AWS. There should not be any other server blocks.

The <code>/health_check</code> localtion block is used to allow my health checks
to hit my server using regular only http. This is the only request that is
allowed to do this. Why am I doing this? A really lame reason. I am a speed
freak and want the health check to be a quick as possible.

The <code>/</code> location block contains no mention of <code>ssl</code>,
certificates, keys, or other. That will be handled in the ELB. What we do want
to do is, with the exception of <code>/health_check</code> redirect any request
whose protocol is not <code>https</code> to <code>https</code>. That is what the
rewrite block does. We use the Strict-Transasport-Security header, which
declares: __"I only speak to agents in TLS/SSL."__


#### ELB as an SSL Termination Proxy ####

Now that we have the Rails app ready for SSL, and the Nginx relinquishing duties
as the SSL termination proxy, we can move on to the AWS console and set up our
ELB.

If you have not created a load balance or would like ot do it all over again,
check out my post from the future:
[Setup ELB as an SSL Termination Proxy]({% post_url 2014-03-26-setup-elb-as-an-ssl-termination-proxy %}).


Find the __Instances__ tab. Click __Edit Instances__ and add or remove all instances, that are
applicable. Check the __Status__, it should say _InService_ if the health check
is working or _OutOfService_ if something is wrong.

If something is wrong, you should begin to feel a six-figure black hole of
"Imposter Syndrome" begin to eat away at your soul. Throw your Mac book out of
the window. Drain your Scottrade account and book it to Waikiki. There is a bar
called Lulu's on the corner of the strip. No one will ever no you failed.

#### Security ####

This would all be pretty crazy, if we didn't secure our instances. Can you
imagine what might happen if we left port 80 open on those instances, heck any
port open on those instances. Scarrryy!!!

Look to the future, for [my post on security groups]({% post_url 2014-03-26-my-big-dumb-security-groups %})

Other than using security groups, check out the googles on security AWS.
