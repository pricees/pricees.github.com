---
layout: post
title: "How to create a self signed ssl certificate"
description: "If its a non-production env, why not?"
category: SSL
tags: [AWS, ELB, SSL, EC2, Security, HTTPS ]
---
{% include JB/setup %}


## The Problem ##

You want to test out your SSL app, but you don't have a cert.  Just make one.
This post was pretty much [ripped from
Heroku](https://devcenter.heroku.com/articles/ssl-certificate-self "Stolen!").

## The Solution ##

If you are using a Windows box, I suggest you google.

For all others, open your favorite terminal program and type:

{% highlight bash %}
$ which openssl || echo "I have's none!"
# should return /path/to/openssl 
{% endhighlight bash %}

If you haves none, install openssl, like so:

__Linux__
{% highlight bash %}
$ [sudo] apt-get install openssl
{% endhighlight bash %}

__Mac OS X__, use [brew](http://brew.sh "Ruby FTW"):
{% highlight bash %}
$ brew install openssl
{% endhighlight bash %}


### Get that key and signing request ###

Copy-pasta the following commands. Filled out the data as you wish.  When you
are asked for a "challenge password []", hit return, leaving the password empty.

_NOTE: I used backslashes to blog readability, only._

{% highlight bash %}
$ openssl genrsa -des3 -passout pass:x \ 
  -out server.pass.key 2048
$ openssl rsa -passin pass:x -in server.pass.key -out \ 
  server.key
$ rm server.pass.key
$ openssl req -new -key server.key -out server.csr
{% endhighlight bash %}

### Get that cert ###

Generate the seif-signed certificate using the _certificate signing request_
server.csr and the  _private key_ server.key

{% highlight bash %}
$ openssl x509 -req \
  -days 365 \ 
  -in server.csr \
  -signkey server.key \ 
  -out server.crt
{% endhighlight bash %}

### Pem Pem Pem goes the trolley ###

For some uses, like, say,
[Using ELB as an SSL termination proxy]({% post_url 2014-03-25-aws-elb-nginx-rails-ssl-the-final-word %}), 
you will need to create a <code>PEM</code> file.

This is is straight forward for our needs, just cat them thangs:
{% highlight bash %}
cat server.key server.crt > server.pem
{% endhighlight bash %}


