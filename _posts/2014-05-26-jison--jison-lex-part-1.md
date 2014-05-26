---
layout: post
title: "Jison & Jison Lex: Part 1"
description: ""
category: 
tags: [jison, jisonlex, parser, lexical grammar, country grammar]
---
{% include JB/setup %}

## Background ##

I am a Ruby guy.
But I really like <a
href="http://www.meetup.com/ChicaGoLang/members/1965082/">Go</a> and
javascript. About four months ago I joined Marc-Andre Couryorner's 
<a href="http://greatcodeclub.com">Great Code Club</a>. Its probably the best
club I have joined in years, next to the StoneCutters. In any event The M-A C,
father of Thin, demonstrates all of his codes in Javascript. Two of 4 of his
projects have used lexers and parsers. This is the impetous for learning
Bison, RACC, and Jison. Regrettably, Bison and RACC, while important, are not
pragmatic to learn at this juncture.

## Background Part 2 ##

I will get back to the Bison blog posts in a minute. For better or worse, I am
going to jump right in to <a href="http://zaach.github.io/jison/">Jison</a>,
Zach Carter's rather awesome javascript lexer parser.

It follow's very closely with Bison in regards to file formatting, declarations,
variables, parsing algorithms, etc. I will not go in to details here.

Over the next series of blog posts, I will attempt to explain lexers and
parsers the Jison way.

You can follow along here: 
<a href="https://github.com/pricees/jison_tutorial/">Ted's Jison tutorial
examples</a>.

I will be splitting up the lexer (.jisonlex) and parser (.jison) for the
purposes of clarity. This may be a bad idea. Putting the lexical grammar into
the head of the parser grammar is rather trivial. I will leave it up to you all
to figure it out.

Lets jump right in!

## Ideas, notes, basics ##

Please read [Bison Part 1]({% post_url 2014-05-11-bison-part-1 %}) for some
other terms.

The basics are this:

We are going two define two types of grammars a lexical grammar, a.k.a.
tokenizer, and a Jison grammar, or parser grammar.

The first file (lexical grammar) is going to tell Jisons lexical analyzer "when you see this
character or group of characters, communicate to the parser generator that you found them
using [this token symbol]." Just so we can deal with this in a trivial manner, just
know that the lexical analyzer reads strings and matches patterns much like
regular expressions.

The second file (parser grammar) says, when the lexical analyzer returns tokens,
match them to the groupings, and output useable code (javascript).

The analogy:

Your eyesballs are a lexical scanner. It sees characters and whitespace, groups
them together and returns the tokens "I got some words." Another part of your
brain says, "okay groups those words together, by punctuation, and distinct
spacing." The useable code in this case is a blog entry you are currently
reading.

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

_nada.jisonlex#L5_ Lexer, I am about to define scanner patterns and tokens

_nada.jisonlex#L7_ Lexer, ignore whitespace. Omitting this is a
rookie mistake. I forget it all the time.

_nada.jisonlex#L8_ Lexier, if the end of the file being parsed is reached,
return the token __EOF__.

_nada.jison#L2_ Jison, I am about to define the parser grammar

_nada.jison#L4_ Jison, I am defining a group "pgm"
_nada.jison#L5_ "pgm" is analyzed whenever we found an end of file token

Modify nada.jison to be the following. 
file.
{% highlight javascript %}
// nada.jison
%%

pgm 
    : EOF            { console.log("Wow! This is wild stuff"); }
    ;
{% endhighlight %}
What do you think will happen? Recompile the parser and parse the text.

## Conclusion ##

You have just witnessed a trivial example of how to build a parser using a
lexical grammar and a parser grammar. How does it feel? We are going to crank this bad boy up to
11 soon, so hold on tight.
