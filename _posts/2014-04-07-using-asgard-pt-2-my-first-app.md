---
layout: post
title: "Using Asgard Pt 2: My first app"
description: ""
category: 
tags: [asgard, ec2, elb, aws, deploy, cloud, scaling]
---
{% include JB/setup %}

### The Backgroup ###

We have Asgard [up and running]({% post_url 2014-04-01-using_asgard_pt_1_up_and_running %}). 
We have an app. Let's go already.

_NOTE: This blog post assumes that we have been able to get our app running on
the AWS which should including creation of the following entities: security
groups, images, instances, elbs, rdses, etc. If not, take a ganders at [Look
Ma!, No Hands]({% post_url 2014-03-18-look-ma-no-hands-on-the-prod-box %}), a
post about how I set up my production AMIs. Then read through the rest of this
blog._

### The Walk through ###

## Setting App ##

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

## AMI ##

The AMI nav bar selection gives you a list of Amazon Machine Images for you to
chose from. I have two or three AMIs from previous blog postings. There are
thousands and thousands of free and for-purchase AMIs that take all the headache
out of setting up an image. Of course, I like the pain. It lets me know that I
am alive.
