---
layout: post
title: "It's not an interview coding problem, it's your first day on the job"
description: ""
category:
tags: []
---
{% include JB/setup %}

Two weeks ago, I was given the opportunity to interview for a premier software development firm, here in Chicago.  I had my resume submitted internally; I was over-excited about the prospect of working for the shop, to say the least.  They were doing everything "right" in their software department: pair-programming, pragmatic agile, open-source work.  They had everything I wanted in compensation: good money, open vacation policy, dental coverage!

I had my phone interview and everything seemed to be on the up and up.  I was sent instructions to development a piece of code that did X, Y, and Z. Piece of cake!  I was told that it generally takes users one to two hours to complete.  No sweat!  I was also told that as soon as I submitted my solution, there was, generally, a one day turn around and then off to the in-office interviews.

I began coding.  I wrote pretty clear, concise code.  I added TomDoc comments for the classes and methods.  I had an entire suite of testing, built on minitest and flexmock.  I was happy with it.  Sure it took 4 or 5 hours to code, refactor, test, and comment, but I wanted to make the reviewers' job as easy as possible.  I submitted the code and waited.

And waited.

And waited.

After a full two days, I received an email.  "We do not wish to move forward with the interview at this time.  If you would like feedback.."

WHAT?!?! (devastation hit me like a sucker punch)

**YES!!! YES, I WANT FEEDBACK, I NEED FEEDBACK!!**

How could I be so wrong?  So diluted?  So dillusional?  So epically ignorant of my own inability to write decent software?  What the hell happened?  Were the blogs I read misinformed?  Was the Github Ruby Style Guide out of favor at this shop? Was I just stupid?  Oh no!!!! NOOO!!!!

I receieved the feedback:

The good:
* The code was easy to follow
* The code was well tested
* The code was well commented

The bad:
* The code contained 'require minitest' as well as 'require minitest/autorun', the former line was unneeded
* The packaging and location of files were in the root directory
* There was no rake file for testing


I responded, hat in hand: "I didn't even consider those, I thought it was about the code"
The HR said: "We leave the instructions vague, but for your experience and the position we expected more."

She was right.

I didn't cross my i's and dot my t's.
I didn't even consider the idea that perhaps organization of code would be taken into account.  ...Because it was just a "stupid interview coding project"....right?

Wait.

No it wasn't.

It was my first day on the job.

My first day on the job!

My first day on the job... I would have scanned an existing project's folders and organization like a table of contents to a great reference book.  I would have taken into this layout into account and would have organized my project accordingly.  I would have not, only, made my program code easy to read, but also supplied scripts to make it easy to test and run.  I would have provided a decent README.  I would have done everything I know to be good developer.  Code is a critical part of what we, as software developers, do; however, its only a part.  The packaging, documentation, etc. make it shippable.

Lesson learned.  Lesson learned.  Lesson learned.

Your software problem is not, merely, an interview checkbox, its your first day on the job.
