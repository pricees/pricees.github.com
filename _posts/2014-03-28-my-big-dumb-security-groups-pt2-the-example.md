---
layout: post
title: "My big dumb security groups pt2, the example"
description: "How to filter traffic through your ELB"
category: 
tags: [elb, ec2, aws, security groups, filter traffic, security, firewall]
---
{% include JB/setup %}

### The Problem ###

In [part one of this post]({% post_url 2014-03-26-my-big-dumb-security-groups %}), 
I covered the attributes of security groups. Security groups are configurable
firewalls for your EC2 resources that are easy to configure and use. 
Like many services in AWS, the optimal use and implementation  often
hinges on nuanced configuration settings. I have found that these  details are
lost in 100s of pages of documentation and google searches.

Below, I will walk you through an example of my setup. I will show you __how I
blocked direct traffic to my EC2 instances, and funneled traffic through my ELB__,
for funzies and security.

Lets go!

_Important Note: If you haven't set up your application and if your application
is not currently accessible, go do that now. These security measures -- if this
is your first rodeo -- should be done last!  I SAID LAST!!_

### Basic Setup ###

