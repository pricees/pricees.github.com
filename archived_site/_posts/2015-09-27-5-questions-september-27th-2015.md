---
layout: post
title: "5 Questions September 27th 2015"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Here's another 5 questions I asked myself this week and my quick findings.

### How many births by city per year ###

I was wondering house many births there are by city per year. Looks like the cdc has data [here](http://www.cdc.gov/nchs/births.htm); find state birth rates [here](http://www.businessinsider.com/cdc-states-with-highest-birth-rates-2014-5). Nothing on cities per se.

### Whats the significance of the Mandelbrot set? ###

A friend at Hashrocket made a comment about Mandelbrot and I was too proud to ask him to elaborate so I decided to look up the Mandelbrot significance. Thanks to Reddit for the answer. What's the significance? Doesn't sound like much.

[hy are Mandelbrot sets so significant? What can they be used for?](https://www.reddit.com/r/askscience/comments/1rlhst/why_are_mandelbrot_sets_so_significant_what_can/)

### How was cocaine discovered? ###

After watching __Narcos__, I wanted to see what the deal was with the drug. Apparently Coca leaves have been used since 3000 BC(E). Cocaine was extracted from the leave, first, in 1859 by a German chist named Alber Niemann. By 1905, there were 5000 US deaths and we banned the substance. And Sigmund Freud loved it. [More here](http://www.drugfreeworld.org/drugfacts/cocaine/a-short-history.html)

### What is unionfs? ###

UnionFS is a stackable unification file system. You can merge contents of different directories. It makes sense by Docker uses this type of file system for building out containers. You can merge/stack different applications, files, etc into your container, make commits, and see the build by its commit history. It's... brillaint. [Check out the Unionfs here](http://unionfs.filesystems.org/)

### What are cgroups? ###

Control Groups, also known as _Cgroups_, are a way to allow the kernel to limit and isolate resource usage (mem, disk space, i/o) for a collection of processes.

[Read the wiki](https://en.wikipedia.org/wiki/Cgroups) or [watch this video](https://sysadmincasts.com/episodes/14-introduction-to-linux-control-groups-cgroups) for more usage.
