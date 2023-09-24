---
layout: post
title: "SSH Forwarding, the parts that I keep forgetting"
description: ""
category: 
tags: [ssh, bash, git, remote login]
---
{% include JB/setup %}

### The Problem ###

For the umpteenth time in a row, I am unable to run my Mina deploy script on a
fresh AWS instance.  Without fail, I keep forgetting the minor, yet important, pieces
to make ssh forwarding work.  So I am posting them here. And adding them to my
init script.

### The Solution ###

{% highlight bash %}
$ ps -e | grep ssh-agent
# Output should look like:
# 2564 ?        00:00:00 ssh-agent
{% endhighlight %}

If the process isn't running, your first inclination might be to run it with the
following command:
{% highlight bash %}
$ ssh-agent
{% endhighlight %}

There is potentially one more step. You may have to add your private key to the
auth agent:

{% highlight bash %}
$ ssh-add ~/.ssh/id_rsa   # other other key
{% endhighlight %}

Now you should be able to do remote operations, like cloning the coolest nodejs
app on github:
{% highlight bash %}
$ ssh -A user@remote "git clone git@github.com:pricees/flash_paper.git"
{% endhighlight %}

But lets get serious, you don't want to type that stuff every time you log in. Lets
add it to the init script.

First, break open that Vim and create a file in your home directory called
".ssh_agent.bash": (ripped from bluegraybox)

{% highlight bash %}
# ~/.ssh_agent.bash
SSH_ENV="$HOME/.ssh/environment"

function start_agent {
    /usr/bin/ssh-agent | sed 's/^echo/#echo/' > "${SSH_ENV}"
    chmod 600 "${SSH_ENV}"
    . "${SSH_ENV}" > /dev/null
    /usr/bin/ssh-add;
}

# Source SSH settings, if applicable
if [ -f "${SSH_ENV}" ]; then
  . "${SSH_ENV}" > /dev/null
  ps -ef | grep ${SSH_AGENT_PID} | grep ssh-agent$ > /dev/null || {
    start_agent;
  }
else
  start_agent;
fi
{% endhighlight %} 

Grant yourself execute permissions for that script:

{% highlight bash %}
$ chmod +x ~/.ssh_agent.bash
{% endhighlight %} 

Once more, break open that Vim, and edit your shell init script (could be
.profile, .bashrc, .bash_profile, etc.).  Add the following at the bottom:

{% highlight bash %}
if [ -f ~/.ssh_agent.bash ]; then
    . ~/.ssh_agent.bash
fi
{% endhighlight %} 

On init, this script will load the ssh-agent and your keys.  You should be good
to go from there.

### Bonus ###

Now, if you really want to be a G, create/edit your ~/.ssh/config and add the
following at the bottom:

{% highlight bash %}
[...]
  Host [ remote host ]
    StrictHostKeyChecking no  # No need to confirm add to host keys
    ForwardAgent yes          # Forward all the things, no -A req on ssh
{% endhighlight %} 

Now you are ready to hack ... on production!


## _Update: ...and one more thing!_ ##

If you attempting to bootstrap a remote server that pulls resources from another remote
location, you may find that your script will not work on account of that pesky host key
verification. 

This looks familiar:

{% highlight bash %}
 ssh ssh-server.example.com   The authenticity of host 'ssh-server.example.com
 (12.18.429.21)' can't be established. RSA key fingerprint is
 98:2e:d7:e0:de:9f:ac:67:28:c2:42:2d:37:16:58:4d. Are you sure you want to
 continue connecting (yes/no)? 
{% endhighlight %}

The remedy is to set the remote profile's ssh config to disable strick host
key checking. Like so:

{% highlight bash %}
# bootstrap
#
# Syntax:
#   ./bootstrap [user@remote] [domain of remote resource]
#
# Example:
#   ./bootstrap ted@remote_server.net github.com
# 
# Description:
#   Searches remote ssh config for an entry matching the 
#   second argument. If entry found, script doesn't 
#   attempt to alter config. If not found, script 
#   places a no strict host key checking option 
#   into you config.

ssh -A $1 <<EOS
  if  [ ! -f ~/.ssh/config ] || [ ! "`grep 'Host $2' ~/.ssh/config`" ];
  then
    echo -e "Host $2\n\tStrictHostKeyChecking no\n" >> ~/.ssh/config
  fi
EOS
{% endhighlight %} 

Using the above script, I could pull my git repo without manually intervention.

{% highlight bash %}
$ ./bootstrap ted@tedsbox.net github.com

$ sudo -A ted@tedsbox.net "sudo apt-get -y install git; git clone git@github.com:pricees/google-drive-companion.git
{% endhighlight %} 

If this doesn't make sense or if you have any issues hit me up on the emails.

Go forth and conquer!
