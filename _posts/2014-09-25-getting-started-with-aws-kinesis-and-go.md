---
layout: post
title: "Getting started with AWS Kinesis and Go"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Wow! I am now working at BradsDeals.com, just got married, and a few other things. Looks like I need to blog more.


# WORK IN PROGRESS #
### Problem ###



### Solution ###

Amazon bills Kinesis as "a fully managed service for real-time processing of streaming data at massive scale."

#### Create the Amazon Kinesis Stream ####

Read the [basic instructions](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/kinesis-pig-create-stream.html)
for creating a data stream or just [go here](https://console.aws.amazon.com/kinesis/home).

Basically:

1. Click "Create Stream"
2. Give the stream a name
3. Set shards* to 2
4. Click "Create"

*A stream is composed of shards. A single shard allows for 2MB/1MB read/write capacity, respectively. 
It also caps the read/write transactions at 5/1000, respectively. 

You should see  your new stream in the table, along with a status of _ACTIVE_ once AWS is done building it.

#### Intro to Sendgrid's Go-Kinesis ####

{% highlight bash %}
# AWS Credentials 
export AWS_ACCESS_KEY="FOO" 
export AWS_SECRET_KEY="BAR" 
export AWS_REGION_NAME="US-EAST-1"
{% endhighlight %}

{% highlight c %}
// producer.go

import (
    "fmt"
    "time"
    "strings"
    "os"

    kinesis "github.com/sendgridlabs/go-kinesis"
)

func main() {
  awsRegion := strings.ToLower(os.Getenv("AWS_REGION_NAME"))
  kregion := kinesis.Region{awsRegion}

  awsAccessKey := os.Getenv("AWS_ACCESS_KEY")
  awsSecretKey := os.Getenv("AWS_SECRET_KEY")

  ksis := kinesis.New(awsAccessKey, awsSecretKey, kregion)

  // CreateStream streamName string, shardCount 2
  streamName := "foo_test"
  shardCount := 2

  ksis.CreateStream(streamName, shardCount)
}

{% endhighlight %}

### Conclusion ###


