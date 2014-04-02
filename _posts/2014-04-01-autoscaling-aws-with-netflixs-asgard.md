---
layout: post
title: "TODO: Autoscaling AWS with Netflix's Asgard"
description: "Netflix, I O U"
category: 
tags: [aws, ec2, netflix, autoscaling, deployment]
---
{% include JB/setup %}

## The Problem ##

AWS is extremely powerful. That power comes with quite a learning curve. As any
nerd will tell you, the power lies in the command line interface, however we
don't have time to do all that stuff.

[Netflix's Asgard](https://github.com/Netflix/asgard/wiki "Cheah!") to the
rescue.

Prerequisites:
- Java 6 or 7 JDK installed
- AWS account
- AWS credentials handy
- AWS account number (see below)
- You have an application you want to deploy 

_NOTE: You don't need to create an SQS or SimpleDB to get Asgard started, these
resources can be created using Asgard_

Wet your beak with this [Asgard presentation](http://www.slideshare.net/pritiman/intro-to-asgard).

Let's roll!

## Getting Real ##

### Authenticate ###

[Download the "asgard-standalone.jar" here](https://netflix.app.box.com/asgard).
The jar has a self-contained Tomcat server

Open the terminal to your download directory and try this on for size:

    java -Xmx1024M -XX:MaxPermSize=128m -jar \ 
    asgard-standalone.jar

Open this in your browser hole: [http://localhost:8080](http://localhost:8080).

Authenticate yourself!

You also need your Amazon account number.

If you focus on the "Summary" tab, find the "User Arn" field. There is a 12
digit number between "arn:aws:iam::" and ":user/", thats your Amazon account
number.

If you want to get jiggy with it [set your config
locally](https://github.com/Netflix/asgard/wiki/Asgard-Configuration).

### SimpleDB &amp; SQS ###

Click "SDB" (SimpleDB) and verify or create a resource with the domain name "CLOUD_APPLICATIONS"

Click "SQS" and create a new queue "Asgard", keep the defaults.

### My First Application ###

There are two logical entities that Asgard refers to that have _no direct
mapping_ to AWS: an application and a cluster.


## Conclusion ##

My first experience with Asgard has given me the feeling that it is the
definitive dashboard and tool for managing cloud configuration and scaling with
my AWS account. I imagine that more of my time will be sent in Asgard than in
the console.

Asgard is just a tool. To use it affectively, I will need to learn more about
AWS and its services. Clearly the subject matter is vast, but the payoff is
invaluable to a shop look to scale.

I will certainly be blogging more about Asgard as I become more comfortable with
AWS.
