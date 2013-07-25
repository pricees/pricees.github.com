---
layout: page
title: I <3 Donuts
tagline: "%w[coding startups productivity]"
meta_tags:
  google-site-verification: "R3N2OpGDpT15GV2SOPN2XrV9FdbrP5J7hDZcSCXl4NY"
---
{% include JB/setup %}

## Contact ##

+ G+ Me [on the GooglePlus](https://plus.google.com/110890498812490682676/posts "Google+")
+ Email Me [on the Gmails](mailto:ted.price-devblog@gmail.com "Gmails")
+ Friend me [on TheFacebooks](https://www.facebook.com/tastethesteel "I gotta delete this thing")
+ Follow me [on the Twitters](http://twitter.com/priceted "I tweet less than I blog")

## Community Events I started ##

+ [ChicaGoLang](http://www.meetup.com/ChicaGoLang/) - GoLang developers in Chicago

## Commercial Endeavors ##

+ [Raise.com](http://raise.com) - Senior Developer
+ [Knowd.com](http://exchange.knowd.com) - Co-Founder & CTO
+ [VersesByPhone.com](http://versesbyphone.com) - Co-Founder

## Open Source Contributions ##

+ [GoogleDriveCompanion](https://github.com/pricees/google-drive-companion) - Easy gdrive access from the command line
+ [MiniGraphdb](https://github.com/pricees/mini_graphdb "MiniGraphdb") - A lightway Graph db written in Ruby
+ [ClassyVoyeur](https://github.com/pricees/classy_voyeur "Classy Voyeur") - Rack App to See Interals of Process
+ ...yeah I am lame with OSS: [Open Source Report Card](http://osrc.dfm.io/pricees)

## Posts to Beat the Band ##

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
