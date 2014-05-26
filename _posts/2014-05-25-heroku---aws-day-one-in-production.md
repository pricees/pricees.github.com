---
layout: post
title: "Migrating from Heroku to AWS: Day One in Production"
description: "Reduced costs, reduced errors, increased deploy time"
category: 
tags: [heroku, aws, infrastructure,]
---
{% include JB/setup %}

## Background ##

I spearheaded the migration off of Heroku onto AWS. It was a great
decision. Here are some of the results.

## The Good ##

We are saving 75% of our budget on servers. We had been running 24 <a
href="https://devcenter.heroku.com/articles/dyno-size">2x dynos</a>
a month. Now, we are running <a
href="https://aws.amazon.com/ec2/instance-types/">two c3.2xlarge
servers</a>. These two servers have
far too much power, we just haven't had the time to "right-size" our AWS
architecture. I imagine a couple of c3.large boxes per availability zone will
do it. These c3.2xlarge servers do not pass 8% CPU usage and are
maxed out around 2gb RAM out of the ~15gb given. Once we get our servers and
auto scaling set, I am quite certain that, even with multi-availability zone
support, we will bring our costs within about 20% of what we had been
paying at Heroku. 

## The Really Good ##

Let me count the ways that AWS has effected our productivity in a postive way:

- No startup thrashing due to timeouts
- No more airbrake eruptions due to unforeseen changes in some layer in
  indirection at Heroku. Airbrakes are showing what we want the to show:
  real breaks in our code base.
- Our 400s and 500s have gone from this type of mess:
<img
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/www-raise-on-heroku-400s-500s.png"
width="600px" />
to this:
<img
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/www-raise-on-aws-400s-500s.png"
width="600px" />
- Pricing has plummetted by 75%, and this is without leveraging
  right-sizing our apps for spinning up during peak times, and spinning down
  at night
- Zero down-time deployment the easy way, using ELBs and health checks
- Easy multi-AZ, multi-region, multi-TLD redundancy and failover
- Security, security, security
- Our customers can tell the difference in the speed of the app

I can tell you that our team members are very happy to not have to worry
about:

- Weekly notices about some part of the Heroku ecosystem having
  troubles
- Airbrakes galore, chasing down timeout related ghost bugs due to
  unrealiable Heroku resource allocation
- Having a dyno, or a few dynos, go down, mysteriously, at night, and end up startup thrashing for 5 hours

## The Ugly ##

There are some downsides to the migration off of Heroku. We have to
implement an alternative to some of their off the shelf, integrated
plugins. Heroku has done an amazing job with 3rd party integration.

  Logging, analytics, and all those goodies can be as good as Heroku;
  however, for day one, we are having to log into a jumpbox and tail and/or grep
  production logs. 
  Clearly this is not what we want to be doing in another month.

## The Really Ugly ##

Heroku was so simple anyone could deploy to production. It is a
beautifully elegant solution to the bane of the developers existence:
devops. 
Push button deployment is so wonderful, that I forgot how agonizing
deployment can be. With AWS, we have to go back to the stoneage. We have gotten the team on the idea of creating new images from a base image, deploying the code, rebooting, and creating images from the app. This takes about 20 minutes if everything goes
well. Its a pain. 

We are aware of Ansible, Fabric, etc. We are not using them as their
deployment pipeline is missing a few steps that we want to employ. 
We are working on <a
href="https://github.com/pricees/rodeo_clown">RodeoClown</a>, a gem to
make our deployment process easy using YAML configs, etc. 

In order to leverage all of AWS's niceties -- autoscaling, failover, etc -- we
have to generate images, and whatnot. This is a small price to pay, in the long run, but
for day one, it is something of a nuisance.


