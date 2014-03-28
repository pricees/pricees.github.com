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

This is the map of my network topography.  The basic setup of my application
servers, close to the live environment, the services it uses, and its
relation to the AWS ecosystem.

Take a ganders at the traffic patterns:
 - The intertubes are hitting my ELB  
 - The ELB hitting EC2 instances
 - EC2 instances are hitting the Mysql RDS server
 - EC2 instances are hitting a Redis custer outside the AWS ecosystem

For the purposes of this blog, I have noted the ports on the map above. If you
are doing it live, you should log find all of the ports you are listening on, or
making outbound connections to.  And right them down on a graph like so:

| Resource | Direction | IP (range?) | Port | 
| -------- | --------- | ----------- | ---- |
| ELB | Inbound | 0.0.0.0/0 (ALL) | 80 |
| ELB | Inbound | 0.0.0.0/0 (ALL) | 443 |
| EC2 (both) | Inbound | (My ELB) | 80 |
| EC2 (both) | Outbound | (Mysql IP) | 11211 |
| EC2 (both) | Outbound | (Redis IP) | 6379 |

Currently, my biggest fear is that some naughty black hat will gain access to my
instances, or services directly. Another fear is that if they gain access, they
will export the data to another service.  I can use security groups to mitigate
this threat.  

1. I will provide myself with SSH access to the services to test that they can
be penetrate (RED)
2. I will create security groups that filter inbound traffic, as well as limit
outbound traffic.
3. I will test all of this security  (GREEN)
4. I will remove the SSH access to the services (REFACTOR...kinda)
