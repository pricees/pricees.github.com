---
layout: post
title: "Forking with Ruby"
description: ""
category: 
tags: []
---
{% include JB/setup %}

## Background ##

The way Unix processes work is rather interesting, and beyond the scope of this blog, but worth the investigation. I will cover the basics.

Every process running in the user space has a parent process. If you follow a processes ancestry you will arrive at the `init` process (__process id of 1__). The `init` process is, itself, a child of the __sched__ or __swapper__ process (__process id 0__) which is a special process run by the kernel.

The way that an application is loaded in Unix is that the current process is `fork`ed. The subprocess inherits all of its parent's __globals__:

- environment variables
- open file descriptors
- data/memory/address space (look up [copy-on-write](https://en.wikipedia.org/wiki/Copy-on-write))
- code/text

The child process get it's own [execution stack](https://en.wikipedia.org/wiki/Call_stack)

After forking, an [exec](http://stackoverflow.com/questions/18351198/what-are-the-uses-of-the-exec-command-in-shell-scripts) call is made and replaces the current code/text and data with a desired application's code and data, etc. This is important information for any coder to know, so know processes, forking and exec inside and out.

Let's take a step back though and look at the Ruby `fork`ing big picture.

## Let Us Code ##
In Ruby, you can create a child a process using [Process#fork](http://ruby-doc.org/core-2.1.2/Process.html) method.

The `fork` method returns twice, once in the parent process and once in the subprocess, also called the _child process_.
The first value returned is an integer, the child process id, which is returned __from the parent process__.
The second value returned is `nil` and returns from the child process.

{% highlight bash %}
$ ruby -e "p fork"
# => 12345  # This is returned from the parent process
# => nil    # This is returned from the child process
{% endhighlight %}

Now that we have that out of the way, let's create a demo script. For this demo, open a second terminal.

{% highlight ruby %}
# myfork.rb
puts "My pid is #$$"

if child_id = fork 
    puts "I am a parent, my id is #$$ and my child is #{child_id}"
else
    puts "I am a child, my id is #$$"
end

gets
{% endhighlight %}

In terminal 1, run the program and let it block:

{% highlight bash %}
$ ruby myfork.rb
My pid is 21830
I am a parent, my id is 21830 and my child is 21859
I am a child, my id is 21859
{% endhighlight %}
 
In terminal 2,  list the processes:

{% highlight ruby %}
$ ps - | grep ruby
pricees  21830 20005  0 11:36 pts/6    00:00:00 ruby myfork.rb
pricees  21859 21830  0 11:36 pts/6    00:00:00 ruby myfork.rb

{% endhighlight %}

_Press a key in the terminal 1 to exit the program_.

Knowing that the parent process returns a truthy-value (integer) and that the child process returns a falsy-value (`nil`), we can use the `fork` method as a predicate to branch between two different execution paths.

{% highlight ruby %}
# File: fork_that_thang.rb

if (child = fork)
    puts "Waiting for child (#{child}) to count to 5"

    # Wait for child process to exit
    Process.waitpid(child) 
else
    (1..5).each do |i|
        sleep 1
        puts i
    end
end

Output:
Waiting for child (6495) to count to 5
1
2
3
4
5
{% endhighlight %}

Sweeeeeeet, right?!

## Conclusion ##

Forking is one of the fundamental system calls to make all applications run in the user space. A developer should understand this system call inside-and-out. Leveraging `fork` in Ruby will allow the developer to create applications of non-trivial complexity. 

Go forth and conquer!
