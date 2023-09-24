---
layout: post
title: "Using Asgard Pt 1: Up and running"
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
rescue....kinda.

Wet your beak with this [Asgard presentation](http://www.slideshare.net/pritiman/intro-to-asgard).

Let's roll!

## The Solution ##

Prerequisites:

- Java 6 or 7 JDK installed
- AWS account
- AWS credentials handy
- AWS account number (see below)
- You have an application you want to deploy 

_NOTE: You don't need to create an SQS or SimpleDB to get Asgard started, these
resources can be created using Asgard_

### Start that thang ###

[Download the "asgard-standalone.jar" here](https://netflix.app.box.com/asgard).
The jar has a self-contained Tomcat server

Open the terminal to your download directory and try this on for size:

{% highlight bash %}
$ java -Xmx1024M -XX:MaxPermSize=128m -jar \ 
  asgard-standalone.jar
{% endhighlight %}

Open this in your browser hole: [http://localhost:8080](http://localhost:8080).

<img style="width: 600px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/asgard_home.png"
/>

### Check yourself ###

Grab your AWS credentials and account number. I didn't have my account number
off hand, I found it this way:

1. Focus on the "Summary" tab 
1. Find the "User Arn" field
1. The 12 digit number between "arn:aws:iam::" and ":user/" is your Amazon account
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

You are ready to move on to [part two of this series]({% post_url 2014-04-07-using-asgard-pt-2-my-first-app %}) of blog posts

My first experience with Asgard has given me the feeling that it is the
definitive dashboard and tool for managing cloud configuration, deployment and scaling with
the AWS infrastructure. I imagine that more of my time will be spent in Asgard than in
the console.

Asgard is just a tool. To use it affectively, I will need to learn more about
AWS and its services. Clearly the subject matter is vast, but the payoff is
invaluable to a any business looking to scale.


