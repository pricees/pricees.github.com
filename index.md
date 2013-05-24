---
layout: page
title: I <3 Donuts
tagline: "%w[coding startups productivity]"
---
{% include JB/setup %}

## Contact ##

+ Friend me [on TheFacebooks](https://www.facebook.com/tastethesteel "I gotta delete this thing")
+ Follow me [on the Twitters](http://twitter.com/priceted "I tweet less than I blog")
+ Email Me [on the Gmails](mailto:ted.price-github@gmail.com "Gmails")


## Commercial Endeavors ##

+ [Knowd.com](http://knowd.com) - Co-Founder & CTO
+ [VersesByPhone](http://versesbyphone.com) - Co-Founder

## Opensource Contributions ##

+ Nothing at this moment....
+ ...Boooooooo....no giving back to the community

## Posts to Beat the Band ##

<ul class="posts">
  {% for post in site.posts %}
    <li><span>{{ post.date | date_to_string }}</span> &raquo; <a href="{{ BASE_PATH }}{{ post.url }}">{{ post.title }}</a></li>
  {% endfor %}
</ul>
