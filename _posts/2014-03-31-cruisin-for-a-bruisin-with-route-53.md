---
layout: post
title: "Cruisin' for a Bruisin' with Route 53: Part One."
description: "Get your DNS straight elastic DNS"
category: 
tags: [AWS, ELB, RT53, EC2, DNS]
---
{% include JB/setup %}

## The Problem ##

Day Zero: The Zombie apacalypse wipes out the one of your data centers. Thats
cool, we got Availability Zone redundency to beat the bad.
Day Two: Zombies take over the region, internet availability goes black.
Day Three: The ".com" TLD is no longer resolved.

Well, if you want high availability Day 2 and Day 3 Zombie Apacalypse
availability, then look no further than
[RT53](https://console.aws.amazon.com/route53/home  "Route 53 on Amazon"), Amazons "elastic" DNS server.

Below should provide you with enough information to make certain that your site is the last
one to go down as society unravels into a chaotic dystopian nightmare.

## The Solution ##


### Get your domain pointing to AWS ###
_You have a domain registered, right? If not, go get one...and don't use GoDaddy
for pete's sake! Use..like...like [gandi](http://www.gandi.net "So hip, they use curse words in their tagline")._

Okay cool.  So open your domain registrar in one tab and RT53 in the other.

In the AWS Console, navigate __Services__ &rarr; __Computer & Networking__
&rarr; __Route 53__

Click "Create Hosted Zone" in the Route 53: Hosted Zones view

Add the "Domain Name:" and a pithy comment.

Create the zone, and then ticket the checkbox next to the new domain if it is
not already. In the right column of the table, you should see the "Hosted Zone
Details". Find the "Delegation Set" (see image). Note those name servers, and
enter them into your domain registrar where applicable. Amazon will be given
authorization to server requests to your domain name.

<img
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/rt53_domain_name_servers.png"
/>

Stop.

Quiet for a second.


Take a moment to admire Amazon's Zombie Apacalypse awareness. Four name servers, four
different top level domains. When dot-com falls, who you gonna calls? Amazon.
Jeff Bezos. Smart dude.

### "A" Record for the whole world to see ###

By default you get two records when you create your domain. 


1. The "SOA" (Start of Authority) record says "Amazon is handling resolution and
request of this here domain"

2. The "NS" (Name Servers) record says "These exact nameservers are responsible for this
domain."

Next we add an "A Record", or Address Record, which response the DNS host/domain
name to an ip address. 

Click "Create Record Set"

Name
: enter subdomain if applicable or leave blank 

Type
: A - IPv4 Address 

Alias
: Yes

Alias Target
: Select a target, I am using my Elastic Load Balancer from the previous blog
entries, so I select that (see below).

Routing Policy
: Simple

Evaluate Target Health
: No

<img
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/rt53_alias_select.png"
/>


Now click "Create".

You should now have a record of type A in your Record Sets. Furthermore, if you
have your EC2, ELB running, your domain should resolve to a running application.

<img 
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/rt53_foo_with_a_card.png"
/>

Success! Ain't life grand?
### Conclusion ###


Using Amazon's RT53 is a natural fit for anyone using the AWS infrastucture.
Part 1 show's us how easily it is to get rockin with Amazons elastic DNS
service.

[In part two]({% post_url 2014-03-31-cruisin-for-a-bruisin-with-route-53-pt-2-fail-overz %}) we discuss
adding a second load balancer, with health checks and failure over.


