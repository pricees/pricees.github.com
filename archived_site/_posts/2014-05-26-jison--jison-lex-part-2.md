---
layout: post
title: "Jison & Jison Lex: Part 2"
description: ""
category: 
tags: []
---
{% include JB/setup %}

## Background ##

Please read [part 1 of this series]({% post_url 2014-05-26-jison--jison-lex-part-1 %}).

You should be comfortable with the lexical and parser grammar files in
01..., 02..., and 03...  folders in <a href="https://github.com/pricees/jison_tutorial/">the jison tutorial github repository</a>.


Lets kick it with <a
href="https://github.com/pricees/jison_tutorial/tree/master/04_say_hello_and_goodbye">
"Say Hello and Goodbye"</a>

## The Codes ##
{% highlight javascript linenos %}
// nada.jisonlex

%%

\s+           /* ignore whitespace, VERY IMPORTANT */

"begin"       return 'BEGIN';
"end"         return "END";
<<EOF>>       return 'EOF';
{% endhighlight %}

The important parts of the lexical grammar are the following:

_nada.jisonlex#L7_ Lexer, if you see the word "begin" return a "BEGIN" token
_nada.jisonlex#L8_ Lexer, if you see the word "end" return a "END" token

{% highlight javascript linenos %}
// nada.jison
%%

pgm 
:           /* whitespace */
| BEGIN     { console.log("We have only just begun."); }
| pgm END   { console.log("Leaving so soon?"); }
| pgm eof
;
eof
: EOF
;
{% endhighlight %}

As you may have guessed the colon (":") tells the parser that _this_ is the first pattern to match for
the "pgm" group. The vertical bar ("|") tells the parser _and here are some
more._
Notice that we use recursive groupings, which translats to something like this:

_A "pgm" group could start with whitespace OR it could start with BEGIN -- which
we know from the lexical grammar is the word "begin". Furthermore, a "pgm" group, having
started with whitespace or "begin", could also have an END token -- or "end".
Lastly, including the previous statements, the "pgm" having been started, and
possibly ended, could have an end of file character._

The important parts of the parser grammar are the following:

_nada.jisonlex#L6_ Parser, when you see the BEGIN token print "We have only just
begun."

_nada.jisonlex#L7_ Parser, when you see the END token print "Leaving so soon?"

Its worth noting line 8 which reads, when you see a pattern matching the group "eof", do
nothing.  You can see on lines 10 & 11 that the group "eof" is defined as the single token
EOF. 

The nada.txt content is the following:
{% highlight bash %}
begin
end
{% endhighlight %}

What do you think would happen if you removed "begin" and ran __make__. Would
the code compile? How might the output change? What if you added more "begin"
and "end" lines to the file? Give it a go!

## Conclusion ##

We have moved forward with our knowledge of the inner-workings of jison and
jisonlex. In part 3 -- whoo! -- its gonna get wild!
