---
layout: post
title: "Implementing Campaigns and CPI"
description: ""
category: database
tags: [database, campaigns, ruby, cpi]
---
{% include JB/setup %}

# The Problem #

At Knowd, Inc. we have been running sponsored ad campaigns for some time.  The problem is that the cost per click ("CPC") has been set at the campaign level.  This has forced us to split up a single campaign into multiple campaigns to ensure that we can effective seperate the clicks into different price segments.  We are now in the midst of re-architectinig the tables.  Furthermore, all things being equal, we should be serving out the most profitable sponsored ads first.  We will be using cost per impression ("CPI") analysis to order these links to match our strategy.

# Solution #

### CAMPAIGNs TABLE ###

The campaign table is where we layout or plan the campaign for a set amount of time.  You have run dates, and a total spend limit.  The max daily spend is the total spend divided by the run length of the campaign.  You assing the links you want to run, and the website that made the purchase.

Campaign Website | Start date | End date   | Total Spend | Max Daily Spend | Links to Run
-------          | ---------- | ---------- | ----------- | --------------- | ------------
AwesomeLinks.com | 01-01-2013 | 01-30-2013 | 30000       | 1000            | Foo, Bar
Clicky.com       | 01-01-2013 | 01-30-2013 | 10000       | 333.33          | Baz, Oof
TopSite.com      | 01-01-2013 | 01-15-2013 | 30000       | 2000.00         | Sweet Link!

### CAMPAIGN SPENDING GOALs TABLE ###

After laying out the campaign table, we drop into a more granular view of the budget.  We want to decide how much money to spend on each link and what cost-per-click each link gives us.  More often then not, these links will be new to our system, so we won't have a good idea of the click-tru-rate, each link enjoys in our ecosystem of Websites.  This is where the experise of our link curators come into play.  They take a best guys at the performance each link will enjoy.  While its important, we stress that this is merely a best-guess that will quickly be validated once the links are released into the wild.
  The latest daily spend values will be used for the website.  As the performance of links becomes more clear, adjustments will be made to maximize revenue, while maintaining a historical record of the previous values for costs-per-click and daily spends on each link.

  In the hypothetical situation below, you can see that before January 5th of 2013, links "Foo" and "Bar" each had a 50/50 split @ $0.05 CPC.  After January 5th, the two had an 80/20 split respectively and the CPCs were lowers and raised respecively.  This might be due in part to the *clickness* of Foo and the *non-clickiness* of Bar.

  The sum total of the for individual daily spends should equality the max daily spend for the campaign.

Effective Date | Campaign | Link | CPC | Daily Spend |
-------------- | ------- | ----- | --- | ----------- |
01-01-2013  | AwesomeLinks.com | Foo     | 0.05 | 500.00
01-01-2013  | AwesomeLinks.com | Bar     | 0.05 | 500.00
01-01-2013  | Clicky.com       | Baz     | 0.03 | 133.33
01-01-2013  | Clicky.com       | Oof     | 0.03 | 200.00
01-01-2013  | TopSite.com      | Sweet Link! | 0.02 |2000.00
**01-05-2013**  | AwesomeLinks.com | Foo     | 0.04 | 800.00
**01-05-2013**  | AwesomeLinks.com | Bar     | 0.08 | 200.00

### CAMPAIGN DAILY GOALs REDIS STORE ###

Building off the previous table, we depart from Postgres land and insert our "link goals" -- the amount of clicks we hope to get daily -- into Redis.  This is stored in a Redis hash.

    GOAL = (DAILY SPEND) / CPC

January 4th, 2013 might look like this:

Link | Daily Click Goal
--- | -----
Foo | 10000
Bar | 10000
Baz | 4444.33
Oof | 6666.66
Sweet Link! | 100,000

January 5th, 2013 might look like this:

Link | Daily Click Goal
--- | -----
**Foo** | 20000
**Bar** | 2500
Baz | 4444.33
Oof | 6666.66
Sweet Link! | 100,000

### CAMPAIGN REPORTs TABLE ###

The Campaign reports table is drawn directly from the Redis data store.  It might look something like this

Date        | Campaign | Link | Total Delivered | Daily delivered
----------- | -------- | ---- | --------------- | ---------------
01-01-2013  | AwesomeLinks.com | Foo     | 10000 | 10000
01-01-2013  | AwesomeLinks.com | Bar     | 2600 |  2600
01-01-2013  | Clicky.com       | Baz     | 4445 | 4445
01-01-2013  | Clicky.com       | Oof     | 6664 | 6664
01-01-2013  | TopSite.com      | Sweet Link! | 100,000 | 100,000
01-02-2013  | AwesomeLinks.com | Foo     | 20000 | 10005
01-02-2013  | AwesomeLinks.com | Bar     | 4950 |  2350
01-02-2013  | Clicky.com       | Baz     | 8889 | 4444
01-02-2013  | Clicky.com       | Oof     | 13330 | 6666
01-02-2013  | TopSite.com      | Sweet Link! | 200,010 | 100,010

### CTRs TABLE ###

After we release these links into the wild, we will begin accumulated stats which will gauge the performance of the links.  The statistics give us a clear understanding of the value of each link to the market.  The *clickier* the link, the more profitable it is to us and to the Website that owns the content.  The reverse is also truth.  We have a click-thru-rate ("CTR") table that allows us to keep track of how "clicky" content is on individual websites, as well as how clicky it is in the system as a whole.  The "\[System Wide\]" is merely the sum total of the clicks divided by the sum total of the impressions for a link, across all websites.  This impression, clicks, ctr value is good for the life of the article.

Link | Clicked On Website | Impressions | Clicks | CTR |
---- | ------------------ | ----------- | ------ | --- |
Foo  | Website A | 100 | 1  | 0.01 |
Foo  | Website B | 100 | 3  | 0.03 |
Foo  | Website C | 100 | 11 | 0.11 |
**Foo**  | **\[System Wide\]** | **300** | **15** | **0.05** |
Bar  | Website B | 400 | 42  | 0.105 |
Bar  | Website C | 100 | 08 | 0.08 |
**Bar**  | **\[System Wide\]** | **500** | **50** | **0.1** |
Oof  | Website A | 10000 | 7500 | 0.75 |
**Oof**  | **\[System Wide\]** | **10000** | **7500** | **0.75** |
Sweet Link! | Website E | 1000 | 500 | 0.5 |
**Sweet Link!**  | **\[System Wide\]** | **1000** | **500** | **0.5** |

### COST PER IMPRESSIONs ("CPI") REDIS HASH ###

Our last bit of data is the costs per impression.  We have a very good understand of the number of impression each of our links serve.  We also have an understanding of the number of clicks they receive.  What we do is multiply by the CPC which allows us to rank the links to display in order of revenue.  Notice that we have a coefficent that takes into account whether we have met the goals set for each link for the day.  If we have, CPI should be set to zero.

    If we have delivered our click goal
      COEFFICIENT = 0
    other wise
      COEFFICIENT = 1

    CPI = CPC * CTR = $/CLICK * CLICK/IMPRESSIONS * COEFFICIENT

A simple examination of the previous links tells us that based on CTR we might use the following order for links, defaulting the the "system wide" ctr to fill in the rest of the order, until adequate data is accumulated to make a practical revenue-driven sort.

Website| Display Order of Sponsored Ads
------ | ------------------------------
Website A | Oof, Foo ...
Website B | Bar, Foo, ...
Website C | Foo, Bar ...
Website E | Sweet Link!, Oof, Bar, Foo

However, January 5th, we modified the CPC, so using CPI to order the websites you would might find the order switch for **Website C**

    CPI of Foo on Website C = 0.11 * 0.04 = 0.0044
    CPI of Bar on Website C = 0.08 * 0.08 = 0.0064

Website| Display Order of Sponsored Ads
------ | ------------------------------
... |  ...
Website C | Bar, Foo...
... |  ...


# RESULTS #

There is a lot of information going on here.  A great deal of time is taking to stage the campaign and guess appropriate values for CPCs for links on different websites.  Soon, however, we start collecting real data and can begin to adjust the campaigns, CPCs and goals toward more accurate values.  By looking at the CPI of each link, we can order and delivered clicks realizing revenue is the most efficient manner as possible, given the data we have.
