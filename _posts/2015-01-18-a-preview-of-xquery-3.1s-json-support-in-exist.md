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
date: 2015-01-18 16:30:00-0500
---

In my mid-2013 article, ["Living in a JSON and OAuth World"](http://joewiz.org/2013/07/04/living-in-an-oauth-json-world/), I discussed the key challenges of talking to web APIs like Twitter and Tumblr from eXist and XQuery, namely:

1. Authenticating with [OAuth](http://en.wikipedia.org/wiki/OAuth) 1.0a
1. Talking to the increasingly [JSON](http://en.wikipedia.org/wiki/JSON)-centric world of web APIs

Well, in the time since that post, these tasks have gotten a lot easier. Let's review what's changed.

First, OAuth 2.0 [did away](https://blog.apigee.com/detail/oauth_differences) with the need for cryptographic request signing and intricate parameter ordering (relying on SSL's inherent security instead), drastically simplifying the task of talking to web APIs. Having only worked with OAuth 1.0a before, I kept thinking that I had to be missing something when I read [GitHub's OAuth API docs](https://developer.github.com/v3/oauth/) this weekend to implement an OAuth client in eXist; I'd authenticated in too few steps, and could submit requests without cryptographic signing? Easily a 50-75% reduction in code and complexity. My, what a boon. 

Second, dealing with JSON is about to get far simpler. The [xqjson library](https://github.com/joewiz/xqjson) that I've been maintaining has been limping along, buoyed by community contributions and yet plagued by [a bug](https://github.com/joewiz/xqjson/issues/14) and the limits of its memory-bound performance. It continues to work for the applications I originally selected it for—parsing Twitter and Tumblr posts as seen [here](http://history.state.gov/)—and I've invested some time in it, adding [a comprehensive test suite](https://github.com/joewiz/xqjson/blob/master/test/xqjson-tests.xql). But feed it a JSON file like [this 40 MB behemoth](https://github.com/textcreationpartnership/Texts/blob/master/TCP.json) (titles and metadata for the 25,000 recently-released early English books from the Text Creation Partnership), and on my system, at least, you'll encounter a java heap space error. It's been great, but these limits made me feel uneasy about relying on it for the long term.

For some time I've been hearing that the W3C's XQuery Working Group was working on a better solution to JSON in XQuery 3.1, currently a [Candidate Recommendation](http://www.w3.org/TR/2014/CR-xquery-31-20141218/), with accompanying [functions and operators spec](http://www.w3.org/TR/xpath-functions-31/). Whereas the xqjson approach was to take all JSON as a string and convert it to XML first before any processing could take place, XQuery 3.1 can natively deal with JSON objects *as* JSON.

For example, instead of writing code like this to extract the text of tweets from JSON:

```xquery
xquery version "3.0";

import module namespace xqjson="http://xqilla.sourceforge.net/lib/xqjson";

let $tweets := xqjson:parse-json(util:binary-to-string(util:binary-doc('/db/user_timeline.json')))/item
for $tweet in $tweets
return
    <tweet-text>{$tweet/pair[@name = 'text']/string()}</tweet-text>
```

... now we can simply write this:

```xquery
xquery version "3.1";

let $tweets := json-doc('/db/user_timeline.json')
for $tweet in $tweets?*
return
    <tweet-text>{$tweet?text}</tweet-text>
```

No external module import, fewer function calls, more streamlined syntax, and direct access to JSON objects. XQuery 3.1 is actually chock full of innovations and labor saving features, but isn't a full recommendation yet. What to do?

Luckily, Wolfgang Meier has begun work on [a new branch of eXist](https://github.com/wolfgangmm/exist/commits/arrays) with support for XQuery 3.1 in its current form. He's selected the [Jackson](http://jackson.codehaus.org/) libary to provide the underlying parsing and serialization of JSON. And he's done a fantastic job adding XQuery 3.1's JSON facilities to eXist. Remember that 40 MB JSON file that caused xqjson to run out of memory?  Wolfgang's XQuery 3.1 branch of eXist can parse it in half a second. In other test where xqjson succeeds in parsing the JSON, Wolfgang's branch beats it hands down.

I've posted some code snippets at GitHub illustrating how to put XQuery 3.1's JSON support to use. The first example, [parse-tweets.xq](https://gist.github.com/joewiz/048489ac436966b17b40), shows how to parse JSON-encoded tweets, extract key fields, and transform them into XML or serialize the transformation as JSON. The second example, [json-util.xqm](https://gist.github.com/joewiz/d986da715facaad633db) is a library module containing utility functions for working with JSON—including pretty printing JSON and transforming JSON into the intermediate XML format defined in the XSLT 3.0 spec. With XQuery 3.1, you can take JSON, turn it into XML, create JSON from scratch, sort and manipulate it, and post it to an external API or JSON document store like [MongoDB](http://www.mongodb.org/). 

Speaking of MongoDB, we can actually add it to this preview of support for the JSON ecosystem in eXist and XQuery. Dannes Wessel's brand new [Mongrel](https://github.com/dizzzz/Mongrel) project allows eXist to talk to MongoDB and GridFS. Dannes promises to adapt his implementation to XQuery 3.1 when it's out. I've taken it for a spin, querying MongoDB using Dannes's excellent [Mongrel documentation](https://github.com/dizzzz/Mongrel/wiki/MongoDB-Examples). MongoDB, via Dannes's Mongrel project, is a perfect complement to eXist's XML database—allowing you to store, query, transform, map-reduce JSON to your heart's content. Furthermore, GridFS via Mongrel gives you the ability to store (or cache) static or binary files much as one would with Amazon S3, in a scalable, responsive way; this could correspondingly relieve eXist of the burden of storing and retrieving these files.

Both Wolfgang's branch and Dannes's project are in active development, and I look forward to seeing them formally released, put to the test, and used in the real world.