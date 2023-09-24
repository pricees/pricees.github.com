---
layout: post
title: "Fun with forks and pipes"
description: ""
category: 
tags: []
---
{% include JB/setup %}

## Background ##

Now might be a good time to read: [Forking with Ruby]({% post_url 2015-09-10-forking-with-ruby %}).

Communication between processes is essential for any non-trivial server tasks. Unix provides a couple of ways to perform interprocess communication: sockets and pipes.

You should know what sockets are, but perhaps I can get to that in a later blog post if you don't. For today, we will use pipes to communicate betwixt processes.

Pipes are special files in Unix; most developers have been explosed to them at the shell level. For our purposes pipes may be treated as elastic, "infinitely-sized" buffers bound only by our system resources. Pipes come in two flavors: anonymous and named.


Here are two examples of _anonymous pipes_ used in a shell:

{% highlight bash %}
$ echo "Hello World" | sed -re 's/([[:lower:]])/\U\1/g'
# => HELLO WORLD
{% endhighlight %}

We are piping the standard output of the `echo` command into the standard input of the `sed` program. The ouput is a screaming "Hello World".

{% highlight bash %}
$ cat the_answer.txt | wc -l
42
{% endhighlight %}

We are piping the standard output of the `cat` command to the standard input of the `wc` command with the `-l` flag set. The output is the number of lines in __the_answer.txt__ file.

Sweet right?

## What about programmatic piping? ##

We use the `IO.pipe` method to create an anonymouse pipe. `IO.pipe` returns a two-element array of IO objects containing a read and write endpoint. 

So, let's say you ran `IO.pipe`, you've create two endpoints in a single process. Where does the other process come from? Well, that is where `fork` comes in. You use fork to create a subprocess and allow the two to communicate via the pipe!

You might wonder how the subprocess can communicate via _a pipe endpoint_ that was opened through the parent, but the subprocess, itself, hasn't opened. Remember, a subprocess inherits all of it's parent's open file descriptors. This means the subprocess can use any pipe's, anonymous or named, that the parent has opened.

The following example is ripped straight from the ruby docs:

{% highlight ruby %}
# anon_pipes.rb
  rd, wr = IO.pipe

  if fork
    wr.close
    puts "Parent got: <#{rd.read}>"
    rd.close
    Process.wait
  else
    rd.close
    puts "Sending message to parent"
    wr.write "Hi Dad"
    wr.close
  end

#Outputs:
#  Sending message to parent
#  Parent got: <Hi Dad>
{% endhighlight %}

__NOTE:__ Pipes by convetioned are to be used unidirectionally. In the previous example, if we wanted to communicate from parent process to the child process, it would be fitting to create another pipe for data flow, _caspice?_

Cool. We got anonymous pipes. What about communicating between two unrelated processes?! I thought you'd never ask! Let's look at _named pipes_.

The user command `mkfifo` makes a __first-in-first-out__ queue, a.k.a. a __named pipe__.

Ruby doesn't have this command, by default, but [there is a gem](https://github.com/shurizzle/ruby-mkfifo) out there for this purpose. For our purposes, we will execute the command in-process. It's a little cavalier, but whatevies.

Let's create the data writer:
{% highlight ruby %}
# pipe_writer.rb
pipe = "/tmp/pied_piper"

`mkfifo #{pipe}` unless File.exists?(pipe)

output = open(pipe, "w+") # the w+ means we don't block
10.times { |n| output.puts n }
output.flush
output.close

puts "Sent data to pipe #{pipe}"
{% endhighlight %}

This script attempts to create a named pipe, if the pipe doesn't exist. We open the pipe (a file) for appending, write some digits, then flush and close the pipe.

What if we run the writer?

{% highlight bash %}
$ ruby pipe_writer.rb
# => Sent data to pipe /tmp/pied_piper
{% endhighlight %}

_If data is written to a pipe without a consumer, did it really happen?_

Lets write a consumer:

{% highlight ruby %}
# pipe_reader.rb
pipe = "/tmp/pied_piper"

`mkfifo #{pipe}` unless File.exists?(pipe)

input = open(pipe, "r") # NOTE: in prod, probably use "r+",
                        # so there is no blocking, EOF errors

while line = input.gets
    puts "Read pipe: #{line}"
end

puts "Read data from pipe #{pipe}"
{% endhighlight %}

If needed we create the named pipe. We open the pipe for reading. In this example we use the flag "r" when opening the file because I want to exit the __while loop__ when the pipe is empty. _Note: in a long lived process I would use "r+" and other control structures._

Lets run the consumer and the writer in individual terminals.

In terminal 1:
{% highlight bash %}
$ ruby pipe_reader.rb
{% endhighlight %}

In terminal 2:
{% highlight bash %}
$ ruby pipe_writer.rb
{% endhighlight %}

The results in terminal 1 should look like the following:

{% highlight bash %}
Read pipe: 0
Read pipe: 1
Read pipe: 2
Read pipe: 3
Read pipe: 4
Read pipe: 5
Read pipe: 6
Read pipe: 7
Read pipe: 8
Read pipe: 9

Read data from pipe /tmp/pied_piper
{% endhighlight %}

Have we got the creative juices flowing on what and how to communicate state between processes?

For grins, list the pipe file using the following command:

{% highlight bash %}
$ ls -lF /tmp/pied_piper
prw-rw-r-- 1 pricees pricees    0 Sep 11 02:15 pied_piper|
{% endhighlight %}

Notice the characteristics of the pipe file:

- it has a size of zero bytes
- the file name has a '\|' (pipe indicator) appended to it (thanks to the `-F` flag)
- the file type in the permissions is 'p' for pipe

## Conclusion ##

There are a few choices when determining the needs for interprocess communication in a POSIX/SUS compliant environment. Pipes offer a performant and easy to use ICP protocol,  when sharing state between to processes that are running _on the same server_. Ruby makes _anonymouse_ pipes each to use out of the box. _Named pipes_ required either a hack or a gem build, but other communication between discrete, unrelated processes.
