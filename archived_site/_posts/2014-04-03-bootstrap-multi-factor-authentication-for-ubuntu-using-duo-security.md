---
layout: post
title: "Bootstrap multi factor authentication for Ubuntu using Duo Security"
description: ""
category: 
tags: []
---
{% include JB/setup %}

## The Problem ##

You want that multi-factor authentication for your ssh hole. 

## The Solution ##

1. Push those pretty little fingers and navigate to [Duo
Security](http://duosecurity.com) and sign. Its like 1000 credits free or
something.
    1. Sign up.
    1. Create a login.
    1. Create an integration.
1. Grab tha script: 
```git clone git@github.com:pricees/ubuntu_duo_mfa.git ```
1. Run the script using the __integration key__, __secret key__, and __API hostname__
from Duo Security:
```$ script/mfa_duo user@host ikey skey apihost```
1. Log into your server and verify that everything is hunky dory.

## Conclusion ##

MFA Rocks! It rocks even more when you have a script.
