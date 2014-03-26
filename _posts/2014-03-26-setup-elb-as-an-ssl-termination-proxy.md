--- 
layout: post
title: "Setup ELB as an SSL Termination Proxy"
description: "How to set up AWS Elastic Load Balancer to handle your SSL needs"
category: 
tags: [AWS, ELB, SSL, EC2, Security, HTTPS ]
---
{% include JB/setup %}

## The Problem ##

Oh the joys of using the Amazons ELB.  Now the heading for this section is "The
Problem," but to be honest, the only problem is that you ain't set that thing up
yet.  ELB is awesome sauce incarnate.  A few mousy pointy clickies and you have
a load balancing monster that handles SSL, port translation, fails overs,
multiple availability zones, and jillions of instances.  Its bliss.  Amazon
where have you been all might life?

So lets get started!

## How to get there ##

<img style="width:580px;"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/EC2%20Management%20Console.png"
/>

The first thing you want to do is navigate to the Load Balancer page.  From the
top left menu:

__Services__ &rarr; __EC2 __ 
Find __Load Balancers __ along the left side menu and click.

... crack your Monster, take a pull

Click __Create Load Balancer__, the wizard will walk you through the details of setting up your load balancer.

### Define that Balancer ###

Fill in the name, then drop down the __Listener Configuration:__ table.
We are configuring this load balancer to be the SSL termination point, so we
will speak to the outside world you HTTPS on port 443.  Internally can speak to
our servers over port 80.

| Load Balancer Protocol | Load Balancer Port | Instance Procotol | Instance Port |
| ------------- | ------------- | ------------- | ------------- |
| HTTP | 80 | HTTP | 80 |
| HTTPS | 443 | HTTPS | 80 |

The app we are serving uses a policy of [Strick Transport Security](http://en.wikipedia.org/wiki/HTTP_Strict_Transport_Security "SSL/TLS Only"); however,
I want to do a permanent redirect at the Nginx level from <code>HTTP</code> &rarr; 
<code>HTTPS</code>.  For this reason, we configure the load balancer to listen
on port 80.

### Selecting Certificate ###

[Click here to create a self signed certificate]({% post_url 2014-03-26-how-to-create-a-self-signed-ssl-certificate %})
 for use with your test, development or stagin' environments. I omit production,
because the only person I know who would do that is
 [one of these guys](http://goo.gl/16JStF).

[Click here to purchase a production
cert](http://www.digicert.com/welcome/ssl-plus-compared.htm "DigitCert
Comparison"). Don't be a cheap ass, this is your
domain's credibility we are talking about here.

Got SSL? Tick the "Upload a new SSL Certificiate" type.

[Follow these instructions to add your certificate](http://docs.aws.amazon.com/ElasticLoadBalancing/latest/DeveloperGuide/US_UpdatingLoadBalancerSSL.html "Amazon's own instructions")

_NOTE: You may use existing SSL certs for future ELBs._

I digress, Continue!

### Cipher ### 

Continue!

### Health Check ### 

The app I am going to run forces connections over HTTPS, so I change the
protocol to HTTPS.  __I do not change the port to 443__ because I am
communicating to my instance over port 80.  _This is an internal health check_.
The path is set "/", the timeouts internals and thresholds stay default.  

Continue!

### Security Group ###

This is pretty important don't you think?  

__In development mode__, I may be cavalier a little about ingress and egress
data, ports and security. I create a group that allows ports 80, 443 from the
world.  Maybe some other ports.  Who knows?  


__In production mode__, I batten down the hatches. The only ports exposed to the
world are 80 and 443. Period.

Oh... security groups? We do that stuff [right cheeya!]({% post_url 2014-03-26-my-big-dumb-security-groups %})

[Click here for more info on AWS security groups](http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-network-security.html
"AWS")

### Adding Instances ### 

Hopefully you have created some instances, if not go do that.  Select the
instances that you want balanced.

Keep the defaults, jerk.

ELB distributes traffice evenly accross zones by default.

Connection draining is the process of allowing existing connections to finish
when you deregister an instance. You can consider this a _graceful
deregistration of your EC2 instance_.  

### Review ### 

Double check your settings and __Create__.

Yeehaw! Time to get TechCrunched cause you just built yourself a Web Scale No Fail
Whale.

### Follow Up ###

Now, just hold on there tubby, we let's peep some of these things.

First and foremost, _contratulations you did it!_

Look at __DNS Name:__ under the __Description__ tab. That is the url you paste
into your world wide web browser.

Under __Instances__ you would see the EC2 instance(s) you are balancing.  If the
_health check_ is set up properly, and the instance and app are running, this
should say "InService".  If it says "OutOfService" you may want hold off on
posting to HackerNews.

The other tabs are fairly straight forward.  If this is a sandbox, go and break
stuff! Otherwise, click around and see what's what.

AWS has a little information on EC2. So if you need assistance or can't sleep,
[check it out here](http://aws.amazon.com/documentation/ec2/ "EC2 Dox Jeah!")
