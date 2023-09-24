---
layout: post
title: "Getting started with AWS Kinesis and Go"
description: ""
category: 
tags: []
---
{% include JB/setup %}

Wow! I am now working at BradsDeals.com, just got married, and a few other things. Looks like I need to blog more.


### Problem ###

At BradsDeals, we looked for a way to streamline out event process.

Currently the process looks something like this:

[Web App] &rarr; [log.tgz files] &rarr; [S3] &rarr; (*) [Redshift] 

After in Redshift:

[Redshift] (*) &rarr; [Postgres Data Mart(s)]

There are some architectural concerns here, but what I seek to eliminate:
- use of cron to poll for new files
- use of logs as intermediary between redshift
- use of redshift as a staging for the data marts

I would like to use a pipeline  

[producer] &rarr; [pipe] &rarr; [consumer]

### Solution ###

__tl;dr__: [peep the code gist](https://gist.github.com/pricees/eaede8224521fee101b4)

We decided to leverage AWS Kinesis for the pipe, and the producer and consumer had to be that Go. The pipeline will look like this:

[Web App] &rarr; [Kinesis] &rarr; [Workers] 

Once we get the data to the workers we can write that the data anywhere:

[Workers] &rarr; [Postgres Data Mart(s)]

[Workers] &rarr; [S3 log files ]

[Workers] &rarr; [Redshift]

[Workers] &rarr; [...other]

What I like about this is that Kinesis can scale vertically to terabytes of data per hour. The Web App and the Workers can scale horizontally. If you need more power than that well... thats why this post is called "Getting started..."

Amazon bills Kinesis as "a fully managed service for real-time processing of streaming data at massive scale."

#### Create the Amazon Kinesis Stream ####

Read the [basic instructions](http://docs.aws.amazon.com/ElasticMapReduce/latest/DeveloperGuide/kinesis-pig-create-stream.html)
for creating a data stream or just [go here](https://console.aws.amazon.com/kinesis/home).

Basically:

1. Click "Create Stream"
2. Give the stream a name
3. Set shards* to 2
4. Click "Create"

*A stream is composed of shards. A single shard allows for 2MB/1MB read/write capacity, respectively.  It also caps the read/write transactions at 5/1000, respectively. 

You should see  your new stream in the table, along with a status of _ACTIVE_ once AWS is done building it.

_Note: the code samples actually create and destroy the stream so you don't need to create the stream, by hand, to run the code_

#### The Codez ####

We are using [Crowdmob's Goamz library](https://github.com/crowdmob/goamz)

Now Kinesis is a byte-stream so we need to use [Go's gob package](http://golang.org/pkg/encoding/gob/) to encode and decode our struct. 

In this example, I have a Car struct.

_Note: I didn't refactor this, so it is not unpolished._

{% highlight bash %}
# Dont forget your AWS credentials 
export AWS_ACCESS_KEY="FOO" 
export AWS_SECRET_KEY="BAR" 
export AWS_REGION_NAME="US-EAST-1"
{% endhighlight %}

{% highlight c %}
package main 

import (
	"fmt"
	"os"
	"strings"
	"time"

	"bytes"
	"encoding/gob"
	"log"

	aws "github.com/crowdmob/goamz/aws"
	kinesis "github.com/crowdmob/goamz/kinesis"
)

type Car struct {
	Id   int
	Make string
}

var (
	maxCars     = 100
	timeOut     = 5
	recordLimit = 100
)
{% endhighlight %}

This should be fairly straight forward. We of important all of the packages required.

We create a Car struct 

We add some variables at are demo-centric:
- the number of cars I am creating
- time out for the main thread to wait for consumers to finish
- max number of records to grab

{% highlight c %}
func main() {

	shards     := 5
	streamName := "test"

	ksis := createStream(streamName, shards)
	defer deleteStream(ksis, streamName)

	streamDescription := waitForActive(ksis, streamName)

	putRecords(ksis, streamName, buildCars())

	for _, shard := range streamDescription.Shards {
		go getRecords(ksis, streamName, shard.ShardId)
	}

	<-time.After(time.Duration(timeOut) * time.Second)
}
{% endhighlight %}

We call a function to attempt to create a new Kinesis stream called "test" with five (5) shards.

We defer a call to delete the stream.

We call a function to put records into the stream, this method calls buildCards to generate a slice of cars

We iterate through the shards of the stream and fire of goroutines to get the records

So that the main thread doesn't exit, we wait for a few seconds. I could have used sleep but whats the fun of that?

{% highlight c %}
func createStream(streamName string, shardCount int) *kinesis.Kinesis {
	region := aws.Regions[strings.ToLower(os.Getenv("AWS_REGION_NAME"))]

	auth, err := aws.EnvAuth()
	if err != nil {
		log.Fatal(err)
	}

	ksis := kinesis.New(auth, region)

	if err = ksis.CreateStream(streamName, shardCount); err != nil {
		fmt.Printf("CreateStream ERROR: %v\n", err)
	}

	return ksis
}

func deleteStream(ksis *kinesis.Kinesis, streamName string) {
	if err := ksis.DeleteStream(streamName); err != nil {
		fmt.Printf("DeleteStream ERROR: %v\n", err)
	}
}
{% endhighlight %}
We grab the region for the stream from the environment variables.
Here we use the EnvAuth method to grab the authentication credentials from the environment vars.

We build the Kinesis object and the attempt to create a stream of a size shardCount

The deleteStream method is straight forward. Send a message to delete the stream or die trying
{% highlight c %}
func waitForActive(ksis *kinesis.Kinesis, streamName string) *kinesis.StreamDescription {
	streamDescription := &kinesis.StreamDescription{}

	timeout := make(chan bool, 30)

	for {

		streamDescription, _ = ksis.DescribeStream(streamName)

		if streamDescription.StreamStatus == "ACTIVE" {
			break
		} else {
			fmt.Printf("Stream be '%s'\n", streamDescription.StreamStatus)
			time.Sleep(4 * time.Second)
			timeout <- true
		}
	}

	return streamDescription
}
{% endhighlight %}

We get a description of the stream and wait 120 seconds for it to become active, otherwise we panic. PANIC!!

{% highlight c %}
func publish(ksis *kinesis.Kinesis, streamName string, collection []*Car) error {
	var partitionKey string
	var buffer bytes.Buffer

	enc := gob.NewEncoder(&buffer)

	for i, item := range collection {
		buffer.Reset()
		enc = gob.NewEncoder(&buffer)
		if err := enc.Encode(item); err != nil {
			log.Fatal(err)
		}

		partitionKey = fmt.Sprintf("partitionKey-%d", i)
		_, err := ksis.PutRecord(streamName, partitionKey, buffer.Bytes(), "", "")

		if err != nil {
			return err
		}

		fmt.Printf("publish: %v\n", item)
	}

	return nil
}
{% endhighlight %}
Pretty ugly ain't it?

We encode the records of the collection use the Gob encode and just them into the stream as bytes

We reset the buffer each iteration so that we don't resend what was previously sent.
{% highlight c %}
func getRecords(ksis *kinesis.Kinesis, streamName, shardId string) {

	shardIteratorRes, _ := ksis.GetShardIterator(shardId, streamName, "TRIM_HORIZON", "")
	shardIterator := shardIteratorRes.ShardIterator

	for {
		records, err := ksis.GetRecords(shardIterator, recordLimit)

		if len(records.Records) > 0 {
			for _, record := range records.Records {
				car := decodeCar(record.Data)
				fmt.Printf("getRecords [%s]: %s\n", shardId, car.String())
			}
		} else if records.NextShardIterator == "" || shardIterator == records.NextShardIterator || err != nil {
			fmt.Printf("GetRecords ERROR: %v\n", err)
			break
		}
		shardIterator = records.NextShardIterator
	}
}

{% endhighlight %}
NOTE: DESCRIPTION NEEDED
{% highlight c %}
func buildCars() (cars []*Car) {
	for i := 0; i < maxCars; i++ {
		cars = append(cars, &Car{i, fmt.Sprintf("Honda %d", i)})
	}
	return cars
}
{% endhighlight %}
Create a slice of pointers to cars, Hondas. Because Hondas are pretty sweet cars.
{% highlight c %}
func decodeCar(data []byte) (car Car) {
	dec := gob.NewDecoder(bytes.NewBuffer(data))
	dec.Decode(&car)
	return car
}
{% endhighlight %}

Decode a car from the byte stream. We use named return values so that we don't need no stinkin variable declaration. We don't use naked returns were I come from, either.

{% highlight c %}
func (c *Car) String() string {
	return fmt.Sprintf("%s [# %d]", c.Make, c.Id)
}
{% endhighlight %}
Print out the car description in a specific format

### Conclusion ###

There you have it, a basic pipeline for passing arbitrary data over Kinesis using Go.

