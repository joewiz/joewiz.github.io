---
layout: post
title: Reflections on learning XQuery
tags:
- xquery
- xml
- learning
- dh
---
Two weeks ago several of my colleagues and I were lucky enough to take a
course on "[XQuery for
Documents](http://www.blackmesatech.com/2013/04/xquery/)" taught by [Michael
Sperberg-McQueen](http://cmsmcq.com/).

I've taken courses on XQuery before—all excellent—but this one was absolutely
unique.

While Michael wasn't directly involved in the working group that produced the
XQuery language, he wasn't far from it—given his involvement with the W3C and
the creation of [XML](http://www.w3.org/TR/REC-xml/) and
[XSD](http://www.w3.org/TR/xmlschema11-1/), not to mention the creation of
[TEI](http://www.tei-c.org/).  Thus, besides just giving an expert
introduction to XQuery, he also shed light on the communities who came
together, with different interests, personalities, politics, and intellectual
frameworks, to create this remarkable language.

My colleagues and I—all historians, some who specialize in the history of
technology—were fascinated by this aspect of the course.  We came away with a
solid foundation in the language, and an appreciation for the milestone it
marks in the history of programming languages and technology.

(The creation of XQuery, as well as the creation of TEI, would be two worthy
dissertation topics.)

Inspired by Michael's course, I've begun thinking about ways to both share my
own appreciation for XQuery and related technologies, tools, and tips that I
have discovered in my own work, and to give this a human dimension, rather
than just a purely instructional one.  I've heard it said that the best camera
is the one that you have with you.  In that spirit, I'll start writing here,
in a blog that I already have.

So, let's begin with a brief introduction about how I came to learn XQuery.

In mid-2007, as a freshly minted history PhD, my new job presented me with the
challenge of revamping a website for a group of venerable historical
publications.  (I've written [an article about the
project](https://letterpress.uchicago.edu/index.php/jdhcs/article/view/80).)
After researching the formats in use for encoding books and historical
documents, I decided to adopt TEI as the format for the publications.   [TEI
P5](http://www.tei-c.org/Guidelines/P5/)—the standard's 5th major version—was
released in October 2007, just in time for my project to adopt it and benefit
from its many advances from the start.

This left the question: how to turn the huge volumes of TEI into a website
that allow historians and the public to view, browse, and search the
publications?  In a superb stroke of luck, I met James Cummings at the [TEI
conference at U Maryland College Park in October
2007](http://www.tei-c.org/Membership/Meetings/2007/) and told him about my
project.  James encouraged me to look into native XML databases.

James' suggestion led me to [eXist-db](http://exist-db.org/).  eXist-db's
Shakespeare demos impressed me with their speed and precision.  Moreover, it
supported XSLT, which would allow me to use the stylesheets I had adapted from
TEI community for turning my XML into HTML for the web.

But besides XSLT, eXist-db also used XQuery for its search and scripting
operations.  [XQuery 1.0](http://www.w3.org/TR/xquery/) had just achieved
recommendation status in January 2007, and Priscilla Walmsley's O'Reilly book
on XQuery was [published in
June](http://shop.oreilly.com/product/9780596006341.do)—the month I graduated.

As I taught myself XQuery with eXist-db (not to mention the indispensable
[oXygen XML Editor](http://oxygenxml.com/)), I found myself ever more
comfortable with XQuery.  Thanks to an invaluable hint in the right direction
by David Sewell (using the same typeswitch-based approach he outlined in [this
mailing list posting](http://developer.marklogic.com/pipermail/general/2008-Au
gust/001706.html) of his), I migrated all of my XSLT routines to XQuery.  Now,
rather than having to master two languages, I could focus on the one that did
everything I needed: XQuery.

Ever since that moment, I've used XQuery almost exclusively, and together with
TEI, eXist-db, and oXygen, it has been my gateway into the world of digital
humanities and software development.  XQuery was challenging to learn but
still very accessible.  The successes have been rewarding.  Even after five
years I am still learning new aspects of the language and uncovering new ways
to apply it.

After several years of being the sole user of XQuery in my office, I'm
thrilled that so many of my colleagues are learning XQuery.  It makes perfect
sense given the amount of TEI and XML that we now have created.  Thanks to
Michael for getting us started, and linking us intellectually to the shared
sense of purpose and possibilities that led to the creation of XML, TEI,
XQuery and the other foundational standards that have enabled and empowered us
to do our work.

I look forward to writing more (probably here, but if elsewhere, I'll post a
link)—for them, and anyone interested in following along.
