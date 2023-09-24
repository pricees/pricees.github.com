---
layout: post
title: "A small bitrate goes a long way"
description: ""
category: misc
tags: [twilio, mp3, audio, latency]
---
{% include JB/setup %}

# The Problem #

VersesByPhone.com recently underwent a radical change in audio.  Instead of using [Ivona](http://ivona.com "Good voices!")\'s text to speech engine for all our verses, we hired voice over actors.  It was a great call.  However, the size of the mp3s were rather large.  After using the new files for some time we noticed was an excessive delay between answering our phone call and the beginning of the verse reading.

# Hypothesis #

  My hypothesis was that the size of the mp3 files were causing a delay somewhere between our CDN over at [MaxCDN.com](http://maxcdn.com "Good service!") and the translation to .wav for telephony over at [Twilio.com](http://twilio.com "Market disruption").

# Test #
  I attempted to modify the sampling frequency because I had read that a typical phone call was about 8000hz.  This degraded the audio quality rediculously, while hardly making an impact on the size of the file.

    $ mpg123 --rate 22000 -w matthew_11_28.22000.mp3 matthew_11_28.mp3
    $ ll
    -rw-rw-r--  1 pricees pricees   1062024 May 30 14:37 matthew_11_28.22000.mp3
    -rw-rw-r--  1 pricees pricees    481802 May 16 15:21 matthew_11_28.mp3

Resampling from 44.1 to 22k increased the size of the mp3 file by 250%.  Clearly, I did something wrong, but I don't have time to investigate, and the sound is a bit degraded, so lets do something radical.

    $ mpg123 --rate 8000 -w matthew_11_28.8000.mp3 matthew_11_28.mp3
    $ ll
    -rw-rw-r--  1 pricees pricees    386220 May 30 14:37 matthew_11_28.8000.mp3
    -rw-rw-r--  1 pricees pricees    481802 May 16 15:21 matthew_11_28.mp3

Resampling from 44.1 to 22k increased the size of the mp3 file by 20%.  The sound quality is unuseable.

This is getting me nowhere.  I decided to change strategies.  Lets reduce the bit rate.

    $ lame --mp3input -b 80 matthew_11_28.mp3 matthew_11_28.80.mp3
    -rw-rw-r--  1 pricees pricees    120960 May 30 14:45 matthew_11_28.80.mp3
    -rw-rw-r--  1 pricees pricees    481802 May 16 15:21 matthew_11_28.mp3

Zoinks! A bitrate of 80 nets us at 75% decrease in size, while maintaining brilliant quality.

    $ lame --mp3input -b 40 matthew_11_28.mp3 matthew_11_28.40.mp3
    -rw-rw-r--  1 pricees pricees     60660 May 30 14:45 matthew_11_28.40.mp3
    -rw-rw-r--  1 pricees pricees    481802 May 16 15:21 matthew_11_28.mp3

A bitrate of 40 nets us at 87% decrease in size, while degrading the quality a noticeable amount.  Lets try a bitrate of 60, for funzies:

    $ lame --mp3input -b 60 matthew_11_28.mp3 matthew_11_28.60.mp3
    -rw-rw-r--  1 pricees pricees     84504 May 30 14:45 matthew_11_28.60.mp3
    -rw-rw-r--  1 pricees pricees    481802 May 16 15:21 matthew_11_28.mp3

Goldilocks!


# Results #

We reduced the size of the media by 80% across the board.  This translates immediately into a win, given that it reduces the bandwidth wasted on our CDN.  The Twilio delay persists.  My assumptions may have been wrong.  I will go email Twilio.
