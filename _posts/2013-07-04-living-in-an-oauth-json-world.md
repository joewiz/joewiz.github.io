---
layout: post
title: Living in an OAuth &amp; JSON World
tags:
- xquery 
- oauth 
- apis 
- json 
- expath 
- gov20 
- twitter 
- socialmedia 
- existdb 
- opensource
---

Another day, [another gist](http://gist.github.com/joewiz/5929809). Today's was prompted by [a question on the eXist-db mailing list](http://markmail.org/thread/5izhua45glftmxja) about how to access OAuth-based services like the Google API with XQuery. I happened to have just been working on accessing the OAuth-based Twitter v1.1 API for the new social media section of [my office's homepage](http://history.state.gov), so I [posted the code](http://gist.github.com/joewiz/5929809) and [some pointers](https://gist.github.com/joewiz/5929809#comment-856692). Like the gist I posted [yesterday](http://joewiz.tumblr.com/post/54546474105/trimming-text-without-cutting-off-words-using-xquery), I hope others can use these bits of XQuery code.

But there's a back story and, dare I say, some illustrative lessons, to this latest addition to my series of posts and gists on XQuery.

Until recently, writing a program to retrieve one's latest tweets was as simple as going to the Twitter homepage is: you just made a basic, unauthenticated HTTP request to Twitter's servers to get the data you needed. But with version 1.1 of Twitter's API, Twitter [announced](https://dev.twitter.com/blog/changes-coming-to-twitter-api) a new requirement - that all requests to its API be signed and authenticated using the [OAuth 1.0](http://tools.ietf.org/html/rfc5849) protocol. This complicated the task of getting data from Twitter exponentially. The OAuth protocol, while not rocket science, requires one to jump through a rather intricate sequence of steps to compose the parameters of your request, and then cryptographically sign the request with a hashing function. (I'm not complaining about the protocol; it does a great job providing an authentication layer to the web. I'm just saying that requiring OAuth to retrieve tweet imposes a pretty heavy burden on users and developers.) If that weren't enough, Twitter also ended support for the XML-based Atom format, leaving just JSON as the format it returned results as. That left me with two problems.

First, XQuery's rich function library does not include the HMAC-SHA1 cryptographic hashing algorithm needed to sign OAuth requests. So I turned to [Claudius Teodorescu](https://twitter.com/ClaudiusTeodore), who applied his considerable Java skills to the task of creating an HMAC-SHA1 function for eXist-db, the XQuery-based server that powers [history.state.gov](http://history.state.gov). We took it a step further, releasing Claudius's work to the EXPath community in the form of an specification: the [EXPath Crypto Module](http://expath.org/spec/crypto). The EXPath community builds up common standards for XPath and XQuery implementations. Claudius also released his module as an EXPath package for eXist-db, which is now available in the [eXist-db Public Package Repository](http://exist-db.org/exist/apps/public-repo/index.html) for anyone to download and install (to do so, go to eXist-db's Dashboard, click on Package Manager, and find "EXPath Cryptographic Module Implementation" in the list of packages"). Look at the [prolog](https://gist.github.com/joewiz/5929809#file-oauth-xq) of the OAuth module I posted in today's gist, and you'll see that it imports Claudius's module.

So I was able to check OAuth off my list of problems.

But besides handling OAuth, I also needed a way to deal with JSON. JSON is increasingly ubiquitous data format in the world of APIs, but its data model is subtly incompatible with XML, and XML-based software like eXist-db has a difficult time ingesting or searching JSON data. Luckily, there were a number of XQuery libraries for me to choose from, and I decided to use one that [John Snelson](http://john.snelson.org.uk/) wrote for [XQilla](http://xqilla.hg.sourceforge.net/hgweb/xqilla/xqilla/file/6458513c94c0/src/functions/XQillaModule.xq). With his permission, I updated it a bit, using some new features in XQuery 3.0 to make his library implementation-independent, and released the updated library [on GitHub](https://github.com/joewiz/xqjson). Thanks to GitHub's mechanisms for code contributions ("pull requests"), the library has already received [several improvements](https://github.com/joewiz/xqjson/graphs/contributors) from the community. The package is also available in [eXist-db's public app repository](http://exist-db.org/exist/apps/public-repo/packages/xqjson.html) and the [CXAN package repository](http://test.cxan.org/pkg/xqjson). (I'm also eagerly following the [JSONiq](http://www.jsoniq.org/) project which is working on extending XQuery to deal natively with JSON, obviating the need to convert JSON to XML to deal with it.)

So I was able to check JSON off my list of problems.  

This paved the way for yesterday's addition of social media links to the homepage of history.state.gov, and coming soon, a complete, searchable social media archive. 

All in all, a story — albeit not unique — of open source communities working together to build solutions to common challenges.

*For more on social media archives in government — the ultimate objective beyond the immediate goal of displaying our latest tweets on our homepage — see NextGov's aptly titled article, [Saving Government Tweets is Tougher Than You Think](http://m.nextgov.com/emerging-tech/2013/06/saving-government-tweets-tougher-you-think/65705/).*