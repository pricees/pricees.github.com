---
layout: post
title: "My big dumb security groups Part 2: the example"
description: "How to filter traffic through your ELB"
category: 
tags: [elb, ec2, aws, security groups, filter traffic, security, firewall]
---
{% include JB/setup %}

_Important Note: If you haven't set up your application and if your application
is not currently accessible, go do that now. These security measures -- if this
is your first rodeo -- should be done only after you have successfully loaded
your app._

## The Problem ##

In [part one of this post]({% post_url 2014-03-26-my-big-dumb-security-groups %}), 
I covered the attributes of security groups. Security groups are, more or less,
firewalls for your EC2 resources that are easy to configure and use. 
Like many services in AWS, the optimal use and implementation  often
hinges on nuanced configuration settings. I have found that these  details are
lost in 100s of pages of documentation and google searches.

Below, I will walk you through an example of my setup. I will show you __how I
blocked direct traffic to my EC2 instances, and funneled traffic through my ELB__
for funzies and security.

Lets go!

## The Solution ##


### Basic Setup ###

<img src="https://docs.google.com/drawings/d/1jSwvaB22e6jU9AEiqLSkvZHzrDT9bhgYXpp9y-bezaQ/pub?w=960&amp;h=450" />

This is the map of my network topography. The basic setup of my application
servers, the services it uses, and its relation to the AWS ecosystem.

Our ideal traffic patterns, the arrows:
 - The intertubes are hitting my ELB  
 - The ELB is hitting EC2 instances
 - EC2 instances are hitting the Mysql RDS server
 - EC2 instances are hitting a 3rd-party Redis service outside of AWS

What we hope to achieve using security groups:
- no client may access any box, without explicity permission
- only HTTP/S traffic allowed through ELB
- any traffic to the EC2 instances must flow through the ELB
- any requests made to the data services must originate from the EC2 instances
- if security is breached, no data may be exported

### The Plan ###

1. I will provide myself with ssh access to applicable boxes
    1. confirm they can be accessed 
    2. see what ports need to be opened
        1. inbound
        2. outbound
2. Create security groups, confirm:
    1. confirm the application can be accessed as expected
    2. confirm the data sources, and 3rd party services are working
3. I will test all of this security, attempt to telnet into boxes, services
4. I will remove the SSH access to the services, and test that

### Get them ports ###

TODO:
1. log in to ec2 instances
2. Run: $ sudo lsof -i
  - write down any ports that read "listen"
  - write down all other ports
  - do not write down ports that are used locally & internally
  - if you don't recognize the port or process, look it up. Don't open ports
    unnecessarily
  
TODO: BLAH BLAH

After jotting down all of the necessary ports, I consider my server setup. It
looks something like this:

<img 
src="https://docs.google.com/drawings/d/1ab8DDSJKRkigdeCZfalgZXATAjyxGYq7q37Un99q42E/pub?w=960&amp;h=450"
/>

Using a cocktail napkin or other handy tool, grid your ports, by resource, for easy
visualization.

| Resource | Direction | IP | Port | Group ID |
| -------- | --------- | -- | ---- | -------- |
| ELB | Inbound | 0.0.0.0/0 | 80 | my-elb-1 |
| ELB | Inbound | 0.0.0.0/0 | 443 | my-elb-1 |
| EC2 | Inbound | ELB | 80 | elb-1-ec2-prod |
| EC2 | Outbound | Mysql IP | 3306 | elb-1-ec2-prod |
| EC2 | Outbound | Memcached IP | 11211 | elb-1-ec2-prod |
| EC2 | Outbound | Redis IP | 6379 | elb-1-ec2-prod |

### Security from the outside in and inside out ###

While I would like to say that we begin on the right side of the diagram, this
is not the case. We will be setting our security on the ELB first, then using
that security group to security the EC2 and RDS. This is the way Amazon wants
its. And what shes wants, she gits.

__Confession: I have 1 security group for development__

During development I use one security group, called
my-dev-grp. This group is applied to my resources. This allows me to
concentrate getting my app and connections running.  When its time to apply
security, I replace those security groups with thier proper counterparts. It
goes without saying that other security measures are considered to secure any
data.

| Resource | Direction | IP | Port | Group ID |
| -------- | --------- | -- | ---- | -------- |
| all | Inbound | 0.0.0.0/0 | all | my-dev-grp |
| all | Outbound | 0.0.0.0/0 | all | my-dev-grp |

__ELB__

Look at our grid. Look at it!! This is rather straight forward. I will create a
security group that allows inbound traffic, from anywhere, on port 80, 443.
There will be no traffic originating from the ELB, so I don't need outbound
traffic open. 

First, create the security group: 

Group name: my-elb-1

Description: ELB security group

Inbound Rules:

| Types | Protocol | Port Range | Source |
| ----- | -------- | ---------- | ------ |
| HTTP | TCP | 80 | 0.0.0.0/0 | 
| HTTPS | TCP | 443| 0.0.0.0/0 |

Outbound Rules:

_This security group has no rules_

Navigate to __Load Balancers__ and click on the __Security Tab__. Click "Edit".
Tick the new "my-elb-1" security group and save.



__Mysql__

I will begin securing the most dependent resources, those on the right side
of my diagram: Mysql, Memcached, and Redis (see note). 

_Note on third-party services:
Our Redis service is provided by a third-party vendor. It is outside our AWS
ecosystem. We have limited control over the security of this service. We must do
our due diligence to ensure that we have configured the Redis service in
accordance with the best practices as directed by the vendor. Furthermore, we
must ensure that the vendor provides a level of security that is within our company's
tolerance level. That is the best we can do._

If I navigate to my RDS dashboard, I find the __Endpoint__ for my mysql
database: _foo.rds.amazonaws.com:3306 (available)_. I can use the following command from
my terminal to verify just how open the server is:

    telnet foo.rds.amazonaws.com 3306
    
    Trying 54.84.167.156...
    Connected to ec2-54-84-167-156.compute-1.amazonaws.com.

The server is open , _OMG!_

Lets close it.
