---
layout: post
title: "Using Asgard Pt 2: My first app"
description: ""
category: 
tags: [asgard, ec2, elb, aws, deploy, cloud, scaling]
---
{% include JB/setup %}

## The Backgroup ##

We have Asgard [up and running]({% post_url 2014-04-01-using_asgard_pt_1_up_and_running %}). 
We have an app. Let's go already.

_NOTE: This blog post assumes that we have been able to get our app running on
the AWS which should including creation of the following entities: security
groups, images, instances, elbs, rdses, etc. If not, take a ganders at [Look
Ma!, No Hands]({% post_url 2014-03-18-look-ma-no-hands-on-the-prod-box %}), a
post about how I set up my production AMIs. Then read through the rest of this
blog._

## The Walk through ##

### Setting App ###

The "App" is a logical entity that exists only in the realm of Asgard. There is
no mapping to an AWS service. These settings reside only as data in SimpleDB.

App &rarr; Applications &rarr; Create New Application

We've seen this before, you know the drill:

<img style="width: 600px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/asgard_new_app"
/>

Fill out the data, click "Create New Application" and we are ready to go.

We see the view page with some of our data, and a few links to instances
and images. If we click instances or images we see blank lists. If not, run! Run
for your life!!

Under the App menu you will find the remaining options:

Stacks
: This list is currently empty. I have no clue what it is, but I have a feeling
that it will involve the Auto-Scale Groups

Owners
: The owerns of different apps, I should see one entry for the app I just
created

Security Groups
: Add, edit, remove your security groups

### AMI ###

The AMI nav bar selection gives us a list of Amazon Machine Images that we may 
chose from. I have two or three AMIs from previous blog postings. There are
thousands and thousands of free and for-purchase AMIs that take all the headache
out of setting up an image. .....of course, I like the pain. It lets me know that I
am alive.

### Cluster ###

Clusters are another logical group that has no mapping to Amazons web service
infrastructure. There is a lot to go over so hold tight. Assume I keep the
defaults, if not explained below. You should read up on them because accepting
the defaults, though you will probably accept the defaults.

Application
: Chose the application you just created, the "Name Preview" should update
automagically. Someone is using javascript!

Instance Bounds
: Choose you minimum. For you max, thinking of how many servers you need on your
best day and triple it. You are going to be a billionaire!

Desired Capacity
: I set this to my minimum, because I don't really know what else I would set it
to.

ASG Health Check Type:
: I am setting this to "ELB" because I want the ELB to check the health of my EC2
instances, and terminate those that fail the health check. I don't know what the
"EC2" option would be good for in production. Its not like I am going to go
terminating EC2 instances willy nilly.

ASG Health Check Grace Period:
: How long does it take my instance & app to boot up? I don't want to start
health checking until that happens. I set this for 240 seconds. If it takes longer
than 4 minutes for my instance and app to boot up, I have bitter problems.

Load Balancer
: I choose the load balancer that I created in a previous blog post. This load
balancer contains my SSL keys and what not.

AMI Image ID:
: Chose the AMI that contains my app.

Instance Type
: Choose what you need. You should know if you app is memory, i/o or cpu bound.
If you don't, roll the dice.

Security Groups
: I chose the groups that I want to be applied to my EC2 instances, i.e.
"my-app-ec2-grp" from the previous posts.

Alright. Click "Create New Auto Scaling Group"

You should now see the "Auto Scaling Group Details" page. Now view your EC2
Instances list in the AWS Console.

<img style="width: 600px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/ec2_instances_from_cluster.png"
/>

Under the Clusters menu you fill find the remaining options:

Auto Scaling Groups
: We can see the group we just created and managed by the cluster. You may
create other groups on this page for the same or other AZs and regions.

Launch Configurations
: Just like it sounds, here are the configurations to get your EC2 instances
running.

Scaling Policies
: Blank so far, but we will soon fill this with the policies that decide whether
to spin-up addition instances or spin-down existings instances.

Scheduled Actions
: No clue what this is just yet. Hopefully this is where the deploy magic
happens.

Cloudwatch Alarms
: Cloudwatch watches your AWS services with checks and alarms. We see what alarm
from our cluster build, however we will create more.

### ELB ###
### EC2 ###
### RDS ###
