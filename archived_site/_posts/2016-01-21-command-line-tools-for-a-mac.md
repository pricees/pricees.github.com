---
layout: post
title: "Command Line Tools for a Mac"
description: ""
category: 
tags: []
---
{% include JB/setup %}
## Command Line Tools for that Macintosh ##

15 Commands in 300 seconds

---

__tee__ - Copy STDIN to STDOUT

---

__caffeinate__ - Keep your computer awake

`$ caffeinate -u -t 300 # keep awake for a 5 mins`
`$ caffeinate -s wget http://example.net/bigfile.xz # keep awake till big file d/l`

---

__lsof__ - list open files

`$ lsof -i # list [i] internet files`

---

__sed__ - A stream editor

`$ cat foo | sed '2,3d; s/Chicago/Los Angeles/g'`

---

__opendiff__ - gui to view/merge two files

---

__top__ / __htop__ - Process / resource monitor

---

__awk__ - Language for scanning and processing text

`$ cat ../foo | awk '{ print $2 }' | sort | uniq`

---

__cut__ - extract characters, fields, etc from input

`$cut -c 2-4 foo`  and
`$ cut -d "," -f 1 foo`

---


__paste__ - join files horizontally to standard output

`paste -d ":" names ages`

---

__join__ - Join two _sorted_ text files along a common field

`$ join ages locales`

---

__nc__ - Tool for reading a writing to network connections. "The Swiss Army Knife" of tcp/udp

`$ nc -l 3000`
`$ while true ; do nc -l 3000 < index.html; done`
`$ curl -X POST -d "Hello=world" "http://0.0.0.0:3000"`

---

__fold__ - Break a line of text

`$ fold -w 10 lorem # break lines from lorem on 10th character` 

---

__comm__ - Complimentary of diff

`$ comm foo bar` 

---

__yes__ - repeats "y" to standard out, good for installs

`$ yes`
`$ yes Centro`

---

__nohup__ - No "hangup"

`$ nohup long_command_on_remote_server &`  

---
__BONUS__

__banner__ - Create a banner of text

`$ banner David`
