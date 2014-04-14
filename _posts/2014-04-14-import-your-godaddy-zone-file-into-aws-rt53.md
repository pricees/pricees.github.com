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
to break away GoDaddy and use Amazon's awesome RT53 elastic DNS service. The
steps for this are:

1. Migrate the zone records from GoDaddy to RT53
2. Repoint the nameservers

## The Solution ##

### Exporting from GoDaddy ###

Sign in to your GoDaddy Account. We will navigate from the "Zone File Editor".

Currently, you can navigate there using the following steps:

Find "My Account" in the nav bar, click it, &rarr; "Manage Your Domains"
&rarr; _click on the domain_ &rarr; _click the "DNS Zone File" tab_ &rarr; click
the "Edit" link located just under the "Zone File" heading. Finally click
"Import/Export" and "Export (Unix)". This will cause _[domain name]_.zone.zip
file to be downloaded to your downloads directory.

### Unzip Inspect the File ###

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

Sign in to AWS and navigate to Services &rarr; Compute & Networking &rarr; Route
53.

Click "Create Hosted Zone", add the proper domain name and a pithy comment.

After creating this hosted zone, you should see it checked in the "Route 53:
Hosted Zones" table. Click the button "Go to Record Sets" for the zone.

From the record sets view, click "Import Zone File". This will create a text
area on the right column.  Open the zone file from the zip archive, above, and
__copy & paste__  all the text under the SOA record into the text area and click
"Import".

_Update_: The image shows that I copied all the comments and the SOA record into
the text area. This was sloppy on my part. Don't import the SOA record or the
comments. Its just for illustration.

<img style="width: 600px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/rt53_record_sets_import_zone_file.png"
/>

It should update successfully.

### What now? ###

Well, test, test, test. Then point your host name registrar to look to the AWS
name servers to resolve your host name and address. Sit back and enjoy the
fruits of your labor.

## The Conclusion ##

Using Rt53 is a no brainer if you have already bought-in and committed to the
AWS infrastructure. Moving the zone records over is the last step in our
migration. Don't repoint your DNS records to RT53 until you have done all
diligent testing.

