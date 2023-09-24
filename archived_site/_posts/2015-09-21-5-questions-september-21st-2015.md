---
layout: post
title: "5 Questions: September 21st 2015"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Five Questions is the ongoing blog post series, that I am attempting to write, that will keep seeking answers to questions that I normally take for granted or simply don't ask.

It's all about having a "beginner's mind."

## What is Kubernetes? ##

[Demistifying Kubernetes](http://www.computerweekly.com/feature/Demystifying-Kubernetes-the-tool-to-manage-Google-scale-workloads-in-the-cloud)


In short, Kubernetes handles container orchestration so that you don't have to. It is one thin layer of abstraction above the vm level. Kubernetes can boot containers on existing VMs, decided that a new VM needs to be spun up and boot a container on that new VM, even decide that a new VM is acting-a-fool and handle it with care.

## What is the Redis HyperLogLog? ##

[What is the HyperLogLog](http://www.tutorialspoint.com/redis/redis_hyperloglog.htm)

The HyperLogLog is an algorithm that uses randomization to approximate the number of unique values in a set. It uses a small constant amount of memory. There is no limit to the number of elements you can count until you approach 2 ^ 64. Wild.

## Ubuntu: How to open any file? ##

Use the command `xdg-open [file]`. Ubuntu will open the file with the default application set.  

I put an alias in my `.bashrc` file.
{% highlight bash %}
$ echo "alias open_file='xdg-open'" >> ~/.bashrc
$ open_file foo.md
# => opens "foo.md" in Mark My Words markdown editor
{% endhighlight %}

## How does a "turbo engine" work? ##

[Video](https://www.youtube.com/watch?v=DqWKNuTppmU)

The turbo charger, through engineering, pulls hot air from the combustion chamber, and feeds cool air to the combustion chamber making the combustion cycle faster and more efficient.

## What are the steps to legally possess a firearm in Chicago? ##

30 Days + $400 and you, too, can possess a firearm in Chicago. [Here are the rules](http://www.chicagogunowners.com/2012/05/step-by-step-chicago-firearm-registration/)



````
