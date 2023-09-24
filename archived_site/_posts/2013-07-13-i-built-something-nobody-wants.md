---
layout: post
title: "I built something nobody wants"
description: ""
category: startups
tags: [startups, lean, tps, versesbyphone]
---
{% include JB/setup %}

## The Problem ##

I built something nobody wants, or should I say, no one will pay for.  Whats the difference right?  I built a web service called [VersesByPhone.com](http://versesbyphone.com).  Its a novel, albeit gimicky, idea.  Every day, a customer receives a phone call and an email, of a different bible verse from the New Testament.  She is given the suggestion to go back to our website and discuss the verse via our Facebook-enabled comments.

Pretty sweet right?  Right?  The problem is: no one will pay for it.  Not $5 a month, not $1 a month.  Zip.  Nada.

## How did we get here? ##

### Flash of Genius ###
In November of 2012, I was riding in the car with my mother when she asked me to read her daily horoscope aloud.  After reading her horoscope, which she has asked me to do on several other occasions, I asked: "Hmm, mom, would you pay to receive a phone call of your horoscope?"

She said: "I might."

The wheels began to turn that night and I got to coding.

I built a really quick proof of concept that used the [Twilio](http://twilio.com) telephony api, which is awesome bee-tee-dub.  Using the Ivona text to speech software, I build a rather robust automated html-scraper-to-text-to-mp3 job.  Using Padrino and these here fingers, this took about 72 hours to get a version 0.0.1.

So I took the plunge: I forked over $1000 to [Ivona Text-to-Speech](http://ivona.com) to get their SaaS services. One thousand dollars purchased 10 million characters.  That would do.  I would surely be rolling in the doh by the following year.

### Pivot, Pivot, Pivot, Cha-Ching ###

I looked around for horoscopes and found a few sites.  I scraped many years of horoscopes and began to translate them.  I began to feel guilty about the thievery.  Generally I say: "Meh, getting sued would only mean I had made it, its a good problem to have."  For better or worse, my conscience got to me.

My next source of "content that people might want to hear in the morning" was numerological readings.  Same problem, copyrighted data sources.  Then I thought, what about inspirational quotes from famous people?  Meh, too lame.

Hmmm... What about bible verses, passages and prayers!  They are free.  Oh snap: can anyone say "gold mine?!"

### Lube Up That Cash Register, Its About To Go To Work! ###

I scraped 30,000 verses from the bible, picked out all that were more than 200 characters, so that they would be meaty, and began translating them via TTS.

I was clearly going places now.  I hired a UI designer/developer on oDesk to whip up a quick skin for my site.  And of course, I got an SEO "jedi knight" to start cranking us up on the Googles.

I began running google adword campaigns.  The word "bible" is expensive.  I ran through my free $150 credit, and then an additional $100.  I can't do this on my own.

Nothing.  I heard feedback, it was not good.  The voices sounded too computer like.  My mother's ears must've been clogged, with love for her son, when she told me that the demo calls she received sounded great.

I didn't know what to do next.  But I knew that this was an amazing product destined for gatrillions in revenue.  I brought on my good friend who was handy with viral traffic.

He got me to finish up the last 10% of the feature list, including hooking up [Stripe](http://stripe.com).  Stripe is awesome.

He and I agreed to tier the subscriptions, add coupons, and lastly, hire voice over actors to read 6 months worth of verses.  They sound really great!  Certainly with all these new features and what not, we could test different schemes to increase out conversions.

Finally it was time...time for advertising.  Stand back everyone! There is going to be a stampede for Verses...by phone! By golly!  We created custom ads and placed them on Christian blogs.

### Greed + Hubris * $0 Dollars = Boo Hoo! ###

We started getting a lot of visitors to our page from the blogs, they worked like a charm.

Our free demo had a 4% conversion, awesome!  We were very happy with that.  For the paid subscription, we had 0% conversion.  We said: give it a week.  But it just stayed that way.  Zero dollars received.

We tried many different devices to attempt to get paid subscriptions:
- 2 different coupon schemes
- 3 different landing pages
- Cutting the form down to minimal inputs
- Merge a 3 page form into one page
- Putting a full fledge demo, including the phone call, on the sign up, so you could try it.

No.  One.  Paid.  Ever.  Whats worse, many people discontinued our free service.

## Conclusion ##

### What I didn''t learn... Yet ###

Why?  Why aren''t people throwing their money at us?!  We must be way ahead of the times, man!  People just don''t understand what we are offering here!  We are literally READING a verse to them aday and then pushing social engagement of the scripture.

All the things we told ourselves didn''t matter.  No one was buying what we were selling.

In fact I couldn''t even get a response of a survey I sent out about what people liked about our product.

The only good thing that came out of this is that I purchased a tablet with the idea that I could go and sell this service at Churches across the globe!!  Oh well.

### All Potential & No Action = A Broke Wantrepreneur... ###

Books I have read in the past 6 months:
- [The Lean Startup](http://theleanstartup.com/) my Eric Ries.
- [Running Lean](http://runninglean.co/) by Ash Maurya.
- [Don''t Just Roll the Dice] (http://neildavidson.com/download/dont-just-roll-the-dice/)

Books I am currently reading:
- [The Four Steps to Epiphany](http://www.amazon.com/The-Four-Steps-Epiphany-Successful/dp/0976470705)
- [Business Model Generation](http://www.businessmodelgeneration.com/)

I love these books.  I am a fan of lean, agile and the Toyota Production System.

However, none of this knowledge means a hill-a-beans if it aint put into action!!

I will never know what people want if I don''t follow the steps outlined in those books.  I will never know anything, unless I get that dang tablet, march out from my apartment, and start interviewing people.

I know how to build something nobody wants.  I aced that part.  Awesome!

Now I need to put in the work and find something that someone does want.

You see, this is not a failure, this service simply didn''t sell.  If I can learn from it, we are all good.

Fin.

