---
published: true
layout: post
title: | 
    A preview of XQuery 3.1's JSON support in eXist
tags: 
  - exist
  - xquery
  - json
  - api
date: 2015-01-18 04:30:00-0500
---

In my mid-2013 article, ["Living in a JSON and OAuth World"](http://joewiz.org/2013/07/04/living-in-an-oauth-json-world/), I discussed the key challenges of talking to web APIs like Twitter and Tumblr from eXist and XQuery:

1. Authenticating with OAuth 1.0/1.1a
1. Parsing the increasingly JSON-only responses from web APIs

Well, in the time since that post, these challenges have gotten a lot easier. Let's review what's changed.

As to the first challenge, Claudius Teodorescu's [crypto library](http://exist-db.org/exist/apps/public-repo/packages/expath-crypto-exist-lib.html) (see the [EXPath spec](http://expath.org/spec/crypto)) is still running very smoothly, and makes handling of cryptographic hashing challenges like HMAC-SHA1 a breeze.  At the same time, OAuth 2.0 did away with the need for cryptographic request signing and intricate parameter ordering (relying much more on SSL instead), which drastically simplified the task of talking to web APIs that support it.  Having only worked with OAuth 1.0/1.1a before, I kept thinking that I had to be missing something when I read [GitHub's OAuth API docs](https://developer.github.com/v3/oauth/) this weekend this weekend; it was just too simple and straight forward!  Easily a 50-75% reduction in code. My, what a boon for developers.

As to the second challenge, the [XQJSON library](https://github.com/joewiz/xqjson) that I've been maintaining has been limping along, buoyed by community contributions but plagued by [a bug](https://github.com/joewiz/xqjson/issues/14) and the limits of its memory-bound performance.  It continues to work for the applications I originally selected it for—parsing tweets and tumblr posts—and I've invested some time in it, adding a comprehensive test suite.  But feed it a JSON file like [this 40 MB bemoth](https://github.com/textcreationpartnership/Texts/blob/master/TCP.json), and on my system, at least, you'll encounter a java heap space error.  It's been great, but these limits made me feel uneasy about relying on it for the long term.

For some time I've been hearing that the W3C XQuery Working Group was working on a better solution to JSON in XQuery 3.1, currently a [Candidate Recommendation](http://www.w3.org/TR/2014/CR-xquery-31-20141218/), with accompanying [functions and operators spec](http://www.w3.org/TR/xpath-functions-31/).  Whereas the XQJSON approach was to take all JSON and convert it to XML first before any processing could take place, XQuery 3.1 can natively deal with JSON objects *as* JSON.

Instead of writing this to parse and query tweets:

{% highlight xquery %}
xquery version "3.0";

import module namespace xqjson="http://xqilla.sourceforge.net/lib/xqjson";

let $tweets := xqjson:parse-json(util:binary-to-string(util:binary-doc('/db/user_timeline.json')))/item
for $tweet in $tweets
return
    <tweet-text>{$tweet/pair[@name = 'text']/string()}</tweet-text>
{% endhighlight %}

Now we can simply write this:

{% highlight xquery %}
xquery version "3.1";

let $tweets := json-doc('/db/user_timeline.json')
for $tweet in $tweets?*
return
    <tweet-text>{$tweet?text}</tweet-text>
{% endhighlight %}

Fewer functions, more streamlined syntax, and direct access to JSON objects.  But XQuery 3.1 isn't a full recommendation yet.  

Luckily, Wolfgang Meier has begun work on [a new branch of eXist](https://github.com/wolfgangmm/exist/commits/arrays) with support for XQuery 3.1.  He's selected the [Jackson](http://jackson.codehaus.org/) libary to provide the underlying parsing and serialization of JSON.  And he's done a fantastic job adding XQuery 3.1's JSON facilities to eXist.  Remember that 40 MB JSON file that caused XQJSON to run out of memory?  Wolfgang's XQuery 3.1 branch of eXist can parse it in half a second.  In my tests, Wolfgang's branch beats XQJSON hands down.

I've posted some code snippets at GitHub to illustrate how to put XQuery 3.1's JSON support to use.  The first example, [parse-tweets.xq](https://gist.github.com/joewiz/048489ac436966b17b40), shows how to parse JSON-encoded tweets, extract key fields, and transform them into XML.  The second example, [json-util.xqm](https://gist.github.com/joewiz/d986da715facaad633db) is a library module containing utility functions for working with JSON—including pretty printing JSON and transforming JSON into the intermediate XML format defined in the XSLT 3.0 spec.  As you can see, both examples focus on taking JSON and turning it into XML.  But you can just as easily generate JSON, either for saving the results of transformed data or posting a query to an external API or JSON document store like MongoDB.  In fact, I'm particularly excited about Dannes Wessel's [Mongrel](https://github.com/dizzzz/Mongrel) project, which allows eXist to talk to MongoDB and GridFS.  Dannes promises to adapt his implementation to XQuery 3.1 when it's out.  Both Wolfgang's branch and Dannes's project are in active development, and I look forward to seeing them formally released, put to the test, and used in the real world.