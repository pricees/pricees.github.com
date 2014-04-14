---
layout: post
title: "Import your GoDaddy Zone file into AWS RT53"
description: ""
category: 
tags: [dns zone files, aws, rt 53]
---
{% include JB/setup %}

## The Problem ##

We are migrating all of our services over to Amazon. One of the final steps is
to break away from GoDaddy's as a DNS and use Amazon's awesome RT53 elastic DNS service. The
steps to this task are:

1. Migrate the zone records from GoDaddy to RT53
2. Point domain name to AWS name servers

## The Solution ##

### Exporting from GoDaddy ###

Sign in to your GoDaddy Account. We will export from the "Zone File Editor"
view. Currently, you can navigate to this destination using the following steps:

Find and click __My Account__ in the nav bar &rarr; click __Manage Your Domains__
&rarr; _click on your domain_ &rarr; _click the __DNS Zone File__ tab_ &rarr; click
the __Edit__ link located just under the __Zone File__ heading. 

To export the file click __Import/Export__ &rarr; __Export (Unix)__. This will
cause "_[domain name]_.zone.zip" file to be downloaded to your downloads directory.

### Unzip., Inspect the Zone File ###

Unzip and examine the zone file. You should see something that looks similar to
this:

{% highlight bash %}
; Domain: foo.com
; Exported (y-m-d hh:mm:ss): 2014-04-14 08:12:10
;
; [ blah blah blah ]
; SOA Record
FOO.COM.        3600    IN      SOA     pdns30.domaincontrol.com.       dns.jomax.net

; A Records
test2   1800    IN      A       166.78.84.192
[...]

; CNAME Records
www     1800    IN      CNAME   @
[...]

; MX Records
@       1800    IN      MX      1       aspmx.l.google.com
[...]


; TXT Records
@       3600    IN      TXT     "google-site-verification=..."

[...]
{% endhighlight %}

We are interested in everything __AFTER__ the SOA (_Start of Authority_) record.

### Import into AWS RT53 ###

Sign in to AWS and navigate to __Services__ &rarr; __Compute & Networking__
&rarr; __Route 53.

Click "Create Hosted Zone", add the proper domain name and a pithy comment.

After creating this hosted zone, you should see it checked in the "Route 53:
Hosted Zones" table. Click the button __Go to Record Sets__.

From the record sets view, click __Import Zone File__. This will create a text
area in the right column.  Open the zone file from the zip archive, above, and
copy & paste all the text under the SOA record into the text area. Click
__Import__.

_Update: The image below shows that I copied some comments and the SOA record into
the text area. This was sloppy on my part. Don't import the SOA record or the
comments, they are just for illustration._

<img style="width: 600px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/rt53_record_sets_import_zone_file.png"
/>

You should receive an "updated successfully" confirmation notice.

### What now? ###

Test, test, test. Then point your domain name to look to the AWS
name servers to resolve your host name and address. This is done through the
registrar. Sit back and enjoy the fruits of your labor.

## The Conclusion ##

Using Rt53 is a no brainer if you have already bought-in and committed to the
AWS infrastructure. Moving the zone records over is the last step in our
migration. Don't repoint your DNS records to RT53 until you have done all
diligent testing.

