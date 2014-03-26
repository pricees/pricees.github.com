---
layout: post
title: "My big dumb security groups"
description: ""
category: 
tags: []
---
{% include JB/setup %}

## The Problem ##

I feel that there are dimenishing returns on the amount of
fun I have, developing, as the project matures.  The first few days, I am in the zone,
hacking, figure out cool things, building out logic, tee-dizzle-dizzle
fo-shizzle.  Then I have to add the front end, meh.  Then third-party payments, ugh!, making it "pretty", possibly writing
browsers tests, and then the worst of the worst: I have to make it secure.

<img style="width: 580px"
src="http://s.quickmeme.com/img/b7/b7f9874f63c8e286f480801395e134a06409ebb52ba634e1d986d7620c0c2cbf.jpg"
/>

Before I was illuminated at the [RSAConf 2014](https://www.rsaconference.com/ "NSA All the Way"), my answer to security was:

+ If its outside the application, its not my problem.
+ If its inside the application, the shared libraries are vetted by the
communities.

Both of these statements are bullsnot.

Security must be of paramount interest to developers in startups and
big-dumb-companies, alike.

Luckily, Amazon offers security groups to handle ingress (inbound) and egress
(outbound) traffic.  

Lets take a ganders at some thangs.

## The Solution ##

From the console, navigate from:

__Services__ &rarr; __EC2__ &rarr; __Security Groups__

I may already have security groups, if I have created a service on Amazon.

Go ahead... click "Create Security Group"

That aint so bad is it?

Group name, description, no big deal. If I have multiple _Virtual Private
Clouds_, I will want to either find out which VPC to use or duplicate the
security group across all of them.

### Inbound Traffic ###

Basically _inbound traffic_ is traffic generated from outside of instances and balances and
whatnot that comes in and hits your app, or services.

__Type__

The protocol used for data. For web servers this will be HTTP or HTTPS.
Selecting from the drop-down will set the protcol and port (range).  Uness you
have good reason, just let Amazon do-whatit-do.

__Source__

Here is the bread and butter of the inbound traffic security.

Anywhere
: Just like it sounds anyone, from anywheres, can access this box

My Ip
: The source box to be filled with  your current public ip. __Only you__ will be
able to access this box.

Custom IP
: Give a custom ip, with a mask, to filter IPs.

### Outbound Traffic ###

Basically _outbound traffic_ is traffic generated from inside your elb setup and
instances. Imagine that a "black hat" got into your system. Well, if you set the
rules that disabled outbound traffic... well then... you add some chicken... baby,
you've got a stew goin!

__Type__

Again, type is pretty self explanatory. What data do you allow to leave. If its
just a web server, batten down the hatches and only set rules for HTTP and
HTTPS. If you are unsure... thinks Waikiki. Again, if you don't know what you
are doing, let AWS set the default protocol and port for you.

Destination

__Source__

Here is the bread and butter of the inbound traffic security.

Anywhere
: Just like it sounds anyone, from anywheres, can receive data from this box

My Ip
: The source box to be filled with  your current public ip. __Only you__ will be
able to receive traffic this box.

Custom IP
: Same as inbound but outbound

