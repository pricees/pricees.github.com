---
layout: post
title: "Verisign SSL on an AWS ELB"
description: ""
category: 
tags: [verisign, ssl, certificate, elb, aws]
---
{% include JB/setup %}

_NOTE: You will also need the private key that you used to create the
CSR (certificate signing request)_

It was a bit trying attempting to figure out how to get our Verisign SSL to install
correctly on our AWS ELB. Here were the steps we used.

## Grab the Certs from Verisign ##

1. Navigate to the certificate page for your domain. Click __Download you
certificate__
<img style="width:580px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/versign_dl_cert_link.png"
/>
2. Select the radio button for "Other server / I don't know" and click
__Download certificate__
<img style="width:580px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/verisign_dl_cert_step2.png"
/>
3. Copy and paste the contents of each box into a seperate text file. _(feel
free to use your real domain in the filenames_

<img style="width:580px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/verisign_keys.png"
/>

| Textarea labeled | Save as local file |
|-------------|------------|
| Primary Intermediate Certificate | intermediate_1.my_domain.crt |
| Secondary Intermediate Certificate | intermediate_2.my_domain.crt |
| End Entity Certicate | public.my_domain.crt|

## Insert into ELB ##

If you are creating a new server, follow the wizard until you get to the SSL
part. Then come to the blog post. I am replacing my existing cert -- __thanks
Heartbleed!__. For this, I find my existing ELB, navigate the "Listeners" tab,
and click the __Change__ link next to my current SSL certificate.

<img style="width:580px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/aws_elb_new_cert.png" 
/>

You should see a form like this:

<img style="width:580px"
src="https://googledrive.com/host/0Bwnu59DLKpNwLWpSS0ZpUzYtZDQ/elb_ssl_form.png"
/>

_NOTE: Notice the textareas read __"pem encoded"__. Assuming that you followed the previous
steps and chose the X.509 format from Verisign, no conversion is necessary_
Give the certificate a name. Possibly include the date for future reference.


_Important Note 1: The order of the certificates matter!! The chain certificate is the __2nd__ intermediate certificate
following immediately by the __1st__ intermediate certificate. This threw me for a
bit._

Make it easier on yourself and create a chained cert file like so.

```
$ cat intermediate_2.my_domain.crt intermediate_1.my_domain.crt  > chain.my_domain.crt
```

_Important Note 2: The certificate chain reads "optional." __It is not
optionall__. We omitted it, and mobile browsers wouldn't accept the cert. Don't
do what I did. Don't be a lazy jerkwad._

Copy and paste the contents of the following local files into the proper text
areas., like so:

| Local file | Textarea labeled |
|------------|------------------|
| [your private key] | Private Key:* |
| public.my_domain.crt | Public Key Certificate:* |
| chain.my_domain.crt | Certificate Chain: |

Click __Save__ and you should be ready to rock and roll.

## Resources ##

<a href="https://knowledge.verisign.com/support/ssl-certificates-support/index?page=content&actp=CROSSLINK&id=AR193">
Verisign SSL Docs</a>

<a href="http://docs.aws.amazon.com/ElasticLoadBalancing/latest/DeveloperGuide/ssl-server-cert.html">
Amazon SSL - ELB Docs</a>

