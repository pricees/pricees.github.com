---
layout: page
title: I <3 Donuts
tagline: "%w[coding startups productivity]"
meta_tags:
  google-site-verification: "R3N2OpGDpT15GV2SOPN2XrV9FdbrP5J7hDZcSCXl4NY"
---
{% include JB/setup %}

## Contact ##

+ G+ Me [GooglePlus](https://plus.google.com/110890498812490682676/posts "Google+")
+ Connect with me on the [Githubs](https://github.com/pricees/)
+ Email Me [on the Gmails](mailto:ted.price-devblog@gmail.com "Gmails")
+ Friend me [on TheFacebooks](https://www.facebook.com/tastethesteel "I gotta delete this thing")
+ Follow me [on the Twitters](http://twitter.com/priceted "I tweet less than I blog")

## Community Events I started ##

+ [SICP Chicago w/ Dave Astels](https://www.eventbrite.com/e/sicp-chicago-w-dave-astels-tickets-15525870296) - We go through "the wizard book" by Dave 
+ [ChicaGoLang](http://www.meetup.com/ChicaGoLang/) - GoLang developers in Chicago

## Conferences where I spoke ##
+ RedisConf 2015 
+ Amoocon 2010

## Commercial Endeavors ##

+ [Bradsdeals.com](http://bradsdeals.com) - Developer -> Engineering Manager  
+ [Raise.com](http://raise.com) - Senior Developer
+ [Knowd.com](http://exchange.knowd.com) - Co-Founder & CTO
+ [VersesByPhone.com](http://versesbyphone.com) - Co-Founder
+ [Owncert.com](http://owncert.com) - Co-Founder

## Open Source Contributions ##

+ [RodeoClown](https://github.com/pricees/rodeo_clown) - Tool for push-button AWS deployments 
+ [FlashPaper](https://github.com/pricees/flash_paper) - Push notes from the
command line to the cloud
using node and firebase
+ [GoogleDriveCompanion](https://github.com/pricees/google-drive-companion) - Easy gdrive access from the command line
+ [MiniGraphdb](https://github.com/pricees/mini_graphdb "MiniGraphdb") - A lightway graph db written in Ruby
+ [ClassyVoyeur](https://github.com/pricees/classy_voyeur "Classy Voyeur") - Rack app to see internals of web app processes
+ ...yeah I am lame with OSS: [Open Source Report Card](http://osrc.dfm.io/pricees)

## Posts to Beat the Band ##

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
