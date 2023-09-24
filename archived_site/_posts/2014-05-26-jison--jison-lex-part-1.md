---
layout: post
title: "Jison & Jison Lex: Part 1"
description: ""
category: 
tags: [jison, jisonlex, parser, lexical grammar, country grammar]
---
{% include JB/setup %}

## Background ##

I am a ruby guy. 
But I really like <a
href="http://www.meetup.com/ChicaGoLang/members/1965082/">go</a> and
javascript. About four months ago, I joined Marc-Andre Couryorner's 
<a href="http://greatcodeclub.com">Great Code Club</a>. It is probably the best
club I have joined in years, next to the StoneCutters. In any event, The M-A C,
father of Thin, demonstrates all of his codes in javascript. Two of 4 of his
projects have used lexers and parsers. I don't know nothing about no lexers and
no parsers. This is the impetous for learning
Bison, RACC, and Jison. My hope is that by explaining Jison, you might be able
to figure out how to use RACC and Rex or Bison and Flex. I will get back to
those blogs if, and when, time allows.

I am going to jump right in to <a href="http://zaach.github.io/jison/">Jison</a>,
Zach Carter's rather awesome javascript lexer parser.

It follow's very closely with Bison in regards to file formatting, declarations,
variables, parsing algorithms, etc. I will not go into the details here.

Over this next series of blog posts, I will attempt to explain lexers and
parsers, the Jison way.

You can follow the basic code here: 
<a href="https://github.com/pricees/jison_tutorial/">Ted's Jison tutorial
examples</a>.

I will be splitting up the lexer (.jisonlex) and parser (.jison) grammars for the
purposes of clarity. This may be a bad idea. Putting the lexical grammar into
the head of the parser grammar is rather trivial. I will leave it up to you all
to figure that out.

Lets jump right in!

## Ideas, notes, basics ##

Please read [Bison Part 1]({% post_url 2014-05-11-bison-part-1 %}) for some
other terms.

We are going two define two types of grammars: a lexical grammar, a.k.a.
tokenizer, and a Jison grammar, or parser grammar. The web is filled with many
other names for these files.

The lexical grammar is going to tell Jison's lexical analyzer _when you see this
character or group of characters, communicate to the parser generator that you found them
using [this token symbol]._ Side note: so we can deal with this in a trivial
manner, for now consider the lexical analyzer nothing more than a regex parser,
that matches patters and returns arbitrary token strings.

The parser grammar says _when the lexical analyzer returns tokens,
match them to the groupings, and output useable code (i.e. javascript)_.

And now for an analogy :

Your eyesballs are a lexical scanner. It sees characters and whitespace, groups
them together and returns the tokens "I got some letters which are probably
words, do something with them." Another part of your
brain says, "okay group of words, I am going to string you together until we hit
a punctuation, and then I am gonna see what we have." The useable code is an
"idea" conveyed through statements in English, like those found in the paragraph
you just read.... so meta!

Of course, if the statement was jibberish, or a foreign tongue, "fadlkfj fad k'lj mnb y." your lexical scanner
would work, your parser grammar might work, but the output would not be useable to a
great extent.

Lets go!

<a href="https://github.com/pricees/jison_tutorial/">Follow along here</a>

## 01 Nothing To See Here ##

Lets just get something compiling, shall we?




{% highlight javascript linenos%}
// nada.jisonlex
/* Tokens go here */
// No tokens for this example

%%

\s+           /* ignore whitespace, VERY IMPORTANT */
<<EOF>>       return 'EOF';
{% endhighlight %}

{% highlight javascript linenos%}
// nada.jison
%%

pgm 
    : EOF            // Some magic should be here
    ;

{% endhighlight %}

{% highlight bash linenos %}
$ touch nada.txt                  # empty file
$ jison nada.jison nada.jisonlex  # parser nada.js is created
$ node nada.js nada.txt
{% endhighlight %}

Did you see it?
__No errors!__ Yeehaw

So lets break this down:

_nada.jisonlex#L5_ JisonLex, I am about to define scanner patterns and tokens.

_nada.jisonlex#L7_ JisonLex, Ignore whitespace. Omitting this is a
rookie mistake. I forget it all the time.

_nada.jisonlex#L8_ JisonLex, If the end of the file being parsed is reached,
return the token __EOF__.

_nada.jison#L2_ Jison, I am about to define the parser grammar

_nada.jison#L4_ Jison, I am defining a group "pgm"
_nada.jison#L5_ "pgm" is analyzed whenever we found an end of file token

Modify _nada.jison_ to be the following:
{% highlight javascript %}
// nada.jison
%%

pgm 
    : EOF            { console.log("zOMG! YoLo!"); }
    ;
{% endhighlight %}
What do you think will happen? Recompile the parser and parse that text.

## Conclusion ##

You have just witnessed a trivial example of how to build a parser using a
lexical grammar and a parser grammar. How does it feel? We are going to crank this bad boy up to
11 soon, so hold on tight.
