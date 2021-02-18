---
published: true
layout: post
title: Fast-backwards
tags:
  - xquery
  - tei
  - dh
  - exist-db
date: 2021-02-18 00:10:00-0500
---

Ever since my daughter learned the word "fast-forward," meaning to skip ahead in a song or TV show, she took to saying "fast-backwards" to mean "rewind." Despite the additional syllables, her phrase instantly took hold in our household. So, given the passage of time since [my previous post](https://joewiz.org/2016/12/11/catching-up/) from December 2016, I think some fast-backwarding is in order! 

> tl;dr [XQuery for Humanists](https://xquery.forhumanists.org) is out! And a roundup of my activities and other developments in the world of XQuery, TEI, and digital humanities.

## The book!

At the end of my last post, I mentioned that I was "wrapping up" work on a book. In retrospect, that characterization was a tad optimistic. Three years later, my co-author, Clifford Anderson, and I met at Circle Bistro in Foggy Bottom to review our *final* set of changes for the manuscript of [XQuery for Humanists](https://xquery.forhumanists.org): 

> We just sent in the final proofs and index for XQuery for Humanists, due out from @TAMUPress—[@andersoncliffb, Dec 10, 2019](https://twitter.com/andersoncliffb/status/1204558089875345409)

[![Joe and Cliff reviewing the XQuery for Humanists manuscript](https://pbs.twimg.com/media/ELdzMW3WoAAzmgp?format=jpg&name=medium)](https://twitter.com/andersoncliffb/status/1204558089875345409)

These meetings with Cliff—in Nashville or DC or at conferences elsewhere, twice a year or so—were the highlights of working on the book. I couldn't have hoped for a better co-author.

At long last, at the end of February 2020, a satisfyingly dense package arrived from Texas A&M University Press. My first book, hot off the presses!

![A stack of copies of XQuery for Humanists, hot off the press](/assets/2020-02-27-books-arrived.jpg)

That box arrived just shy of one year ago—just as COVID-19 began to ravage the world. While the pandemic and other events of 2020 absorbed much of the energy I had planned to put into the book's launch, I am so pleased with the reception the book has received, and especially that it has reached distant shores and entered classrooms. Here are some selected tweets from readers:

> Y’all. This excellent book by @andersoncliffb and @joewiz arrived in mid March. I intended to tweet this photo on the last Friday I was in my office. And then everything happened. So I’m posting it now and saying I’m really glad I brought it home with me—[@KathrynTomasek, May 22, 2020](https://twitter.com/KathrynTomasek/status/1263910398987833348)

> I liked the book very much it’s extremely well written—[@ZornGerhard, Sep 19, 2020](https://twitter.com/ZornGerhard/status/1307333258745712642)

> Your book was one of five introductions to XQuery that I especially recommended to my class on Wednesday. ;-)—[@MonikaBarget, Nov 19, 2020](https://twitter.com/MonikaBarget/status/1329544401232814081)

> The moment when #XQuery does not work and you remember the magic of *: from #XqueryForHumanists :). Great book!—[@CatoMinor3, Dec 4, 2020](https://twitter.com/CatoMinor3/status/1334934349507162112)

## The companion website

Whether you're a reader of the book, just curious about it, or a seasoned user of XQuery, I hope you have explored the book's [companion website](https://xquery.forhumanists.org). To make it I used several technologies familiar to readers of the book and this blog. Naturally, I used XQuery to prepare the site's [Code section](https://xquery.forhumanists.org/code/) by transforming the plain text source files into GitHub-flavored Markdown. I also used XQuery to validate that all of the sample code was free of syntax and well-formedness errors and would run in the three XQuery processors we highlighted in the book. We addressed processor-specific issues in our [Implementations page](https://xquery.forhumanists.org/implementations/). We hope the site is a useful resource for all learners of XQuery. 

Under the hood, the book's companion website is a static site powered by Jekyll and hosted by GitHub. My main criterion was good syntax highlighting for support for XQuery, XML, XSLT, and JSON. [Rouge](http://rouge.jneen.net/), which Jekyll has used as its default syntax highlighting library since version 3, does a great job on this front; see the sample [XQuery page](http://rouge.jneen.net/v3.26.0/xquery/). As a result, both the companion site and this blog now run on Jekyll, version 4. GitHub Pages's support for HTTPS on custom domains such [xquery.forhumanists.org](https://xquery.forhumanists.org) and [joewiz.org](https://joewiz.org) made it a great platform to host the site's content.

## Other projects and news

Between the book and other developments, there's now a lot to catch up on in the sphere of XQuery, XML, and digital humanities. So let's fast backwards, with a sampling of papers, packages, and projects, in roughly reverse chronological order:

- I co-authored and co-presented a paper at Balisage 2019 with David J. Birnbaum, Hugh Cayless, Emmanuelle Morlock, and Leif-Jöran Olsson: [The integration of XML databases and content management systems in digital editions: Understanding eXist-db through Reese’s Peanut Butter Cups](http://www.balisage.net/Proceedings/vol23/html/Birnbaum01/BalisageVol23-Birnbaum01.html). This is one of the first papers to survey and evaluate different approaches to application development with XQuery; it's focused on developing digital editions but is widely applicable to other domains.

- I co-authored a paper with Christiane Sibille based on our presentation at ICEDD Berlin 2019: [Best practices for digital diplomatic documentary editions](https://diplomatic-documents.org/best-practices/digital-editions/). A brief handbook of lessons from Dodis and FRUS for ICEDD editions to consider as they develop a digital publication strategy. We encouraged adoption of open standards like TEI and listed options for training, tools (including TEI Publisher, discussed more below), etc. 

- Cliff and I gave two half-day workshops on XQuery at [DH2017](https://dh2017.adho.org/) in Montreal: [XQuery for Digital Humanists](https://dh-abstracts.library.cmu.edu/works/4183) and [XQuery for Data Integration](https://dh-abstracts.library.cmu.edu/works/4190). These presentations gave an introduction to XQuery and covered advanced uses for digital research. See [the lecture notes and code](https://github.com/CliffordAnderson/XQuery4Humanists). 

- XQuery 3.1, which enabled so much of our book and the DH2017 workshops, became [a W3C Recommendation](https://www.w3.org/TR/xquery-31/) on March 21, 2017. It was great to have the finalized spec in place as we prepared the manuscript and the workshops. Our thanks again to the editors and the working groups for all of their efforts on this fantastic family of specifications.

- Outside of conferences and this blog, I posted articles and useful code snippets—on [my GitHub Gist page](https://gist.github.com/joewiz). Articles include: 
    - [Converting an eXist application from old-style fields to new, Lucene-based facets and fields](https://gist.github.com/joewiz/99e5fa283086e4bf74fe8143cab3f92c) 
    - [How variables in XQuery FLWOR expressions change when using the `group by` clause](https://gist.github.com/joewiz/e9d83632a6a056976e5b5fff7c49d6d4)
    - [Basic dynamic web pages, with XQuery and eXist](https://gist.github.com/joewiz/69e767a20d32a22ffa37da95d75457b7)
    - [An introduction to recursion in XQuery](https://gist.github.com/joewiz/6762f1d8826fc291c3884cce3634eb77)
    - [Full text search of Chinese text, with eXist-db and Lucene](https://gist.github.com/joewiz/49b6598790d973a557f4eaf68e1f9ca9). 
    
    (With better XQuery syntax highlighting in Jekyll now, perhaps it's time to move these articles over here and to develop some of the other code snippets into proper posts. ICYMI, Gist recently gained a long-requested feature: you'll now receive notifications when a reader leaves a comment on your Gist. Before this, readers could post comments... but the authors would never know.)

- One of my gists graduated to a full-fledged library package: [semver.xq](https://github.com/eXist-db/semver.xq). You can use it to validate, compare, sort, parse, and serialize [Semantic Versioning](https://semver.org/) version strings. This package is now part of the eXist distribution, where it is used for EXPath package libraries. But you can use it for anything you like.

- Speaking of XQuery packages, I published two new ones last week: [airtable.xq](https://github.com/joewiz/airtable.xq), a library for accessing the Airtable REST API; and [Airlock](https://github.com/joewiz/airlock), an application for taking snapshots of Airtable bases for offline browsing and transformation. If you're not familiar with it, [Airtable](https://airtable.com) is a web-based relational database and a great way for teams to collaborate on data with interlinked records. It's makes for a nice pairing with the world of XML documents and databases. My colleagues have been using it to manage metadata associated with our documents and model relationships between entities like people, places, and organizations. I chose the name "Airlock" since, like its [counterpart in the physical world](https://en.wikipedia.org/wiki/Airlock), Airlock permits the passage of data between relational and document models, minimizing mismatches and maximizing the advantages of the environments on both sides for managing and manipulating data.

- I also updated the venerable [Punch](https://github.com/joewiz/punch) app package, which I originally created to accompany the eXist workshops I gave at [TEI@Oxford 2010](https://web.archive.org/web/20161212215447/http://tei.oucs.ox.ac.uk/Talks/2010-07-oxford/workshops.xml) and [Digital.Humanities@Oxford Summer School 2011](https://web.archive.org/web/20121030181142/http://digital.humanities.ox.ac.uk/dhoxss/2011/sessions.html). It demonstrates how to create a dynamic website for a TEI edition using XQuery and eXist, in a world from before TEI Publisher. It's still a good illustration of what you'd need to create an edition from scratch—as discussed in the Balisage paper above.

- I happily abandoned [xqjson](https://github.com/joewiz/xqjson) once eXist added support for XQuery 3.1's JSON parsing and serialization capabilities. 

- I've received contributions and posted updates for two lists of XQuery resources that I maintain:
    - [Learn XQuery: A list of great articles, blog posts, and books for learning XQuery](https://github.com/joewiz/learn-xquery)
    - [XQuery Power: A list of resources built on or with XQuery](https://github.com/joewiz/xquery-power).

- I've kept the [@XQuery](https://twitter.com/XQuery) Twitter account kicking, mostly with retweets of posts from other users but occasionally with posts. (: Let's make it to 400 followers in 2021! :) 

- Some great XQuery questions have come up on Stack Overflow; here's the list of [questions I've answered](https://stackoverflow.com/users/659732/joewiz?tab=answers). Most of the time, though, Martin Honnen beats me to the punch, and puts his XQuery Fiddle tool (see below) to great use.

Outside of my own projects, numerous tools and projects have entered the scene and become invaluable, among which include: 

- [TEI Publisher](https://teipublisher.com/), created by Wolfgang Meier and now developed under e-editiones, lowers barriers and empowers TEI projects, by ingeniously leveraging the TEI Processing Model and establishing a common framework for digital editions. I discuss TEI Publisher in the Balisage and ICEDD papers linked above. Continuing the work of the late Sebastian Rahtz, TEI Publisher's community is active and rapidly growing.
- [existdb-vscode](https://marketplace.visualstudio.com/items?itemName=eXist-db.existdb-vscode), by Wolfgang Meier, brings great XQuery support to Visual Studio Code.
- [generator-exist](https://github.com/eXist-db/generator-exist), by Duncan Paterson, simplifies the task of generating scaffolding for library and application EXPath Packages for eXist. I used this when I prepared airtable.xq and Airlock for publishing.
- [XQuery Fiddle](http://xqueryfiddle.liberty-development.net/), by Martin Honnen, excels at sharing XQuery snippets and is used widely in Stack Overflow answers.
- [Dash docsets](https://dash.nwalsh.com/), by Norm Walsh, makes searching the W3C XML and XQuery specifications a snap.
- [exist-stanford-nlp](https://github.com/lcahlander/exist-stanford-nlp), by Loren Cahlander, brings the Stanford CoreNLP library for named multilingual entity recognition and other natural language processing capabilities like parts of speech tagging and sentence tokenization to XQuery and eXist. Here's some [sample code](https://gist.github.com/joewiz/0a7e1f9b69169fc907fb482d28f24947) I wrote.
- [github-xq](https://github.com/eXist-db/github-xq), by Winona Salesky, lets you interact with the GitHub API from XQuery—making commits, submitting pull requests, creating branches, and responding to webhooks.

## XQuery implementations and the community

In addition, the XQuery implementation that I've used on a daily basis, [eXist](https://exist-db.org), has leaped from version 3 to 4 and now 5, bringing extensive XQuery 3.1 support, improvements in performance and stability, and transformative features like [facets and fields](https://exist-db.org/exist/apps/doc/lucene#facets-and-fields). eXist has modernized greatly, jettisoning its legacy build process with a standard Maven build process; you can check eXist's conformance with the W3C's XQuery test suite using the [exist-xqts-runner](https://github.com/eXist-db/exist-xqts-runner); and you can use it with [Docker and continuous integration pipelines](https://exist-db.org/exist/apps/doc/integration-testing), including GitHub Actions.

> Agreed, the facets & fields facility in eXist 5 has made me rethink search, sort, and other aspects of my XQuery design. The ability to pre-generate facet & field contents means you can skip a lot of XQuery processing at request time. It’s like a customized, persistent cache.—[@joewiz, May 1, 2020](https://twitter.com/joewiz/status/1256346156034113537?s=20)

While eXist 5.2 has been out for nearly a year, don't mistake this for a lack of activity; the core developers and community contributors have put considerable energy into new features and bugfixes, and the next version—more likely version 6 than 5.3—is shaping up to be the best yet. 

In these past few years, the eXist community has undergone a renaissance, with a very active Slack channel and weekly community calls (see [these calendar and invite links](https://github.com/eXist-db/exist#open-community-calls)). It's exciting to see talented new users with great DH projects. This community also has a good deal of overlap with the [e-editiones](https://e-editiones.org) and TEI Publisher community, which also has a great Slack channel (see [this invite link](https://e-editiones.slack.com/join/shared_invite/zt-e19jc03q-OFaVni~_lh6emSHen6pswg#/)).

Like eXist, [BaseX](http://basex.org/), [Saxon](https://saxonica.com), and [oXygen XML Editor](https://www.oxygenxml.com) have issued a steady stream of stellar releases with great support for XQuery 3.1. These products all are indispensable tools in my toolbox. 

Two new XQuery implementations, [xqerl](https://github.com/zadean/xqerl) and [FusionDB](https://fusiondb.com), have entered the scene and look to have a great future. For eXist users, FusionDB is compatible with eXist, and [Fusion Studio](https://github.com/evolvedbinary/fusion-studio) is a promising IDE and administrative tool that could consolidate many of the tools that eXist users rely on: eXide, Monex, the Java admin client, and possibly more.

Looking ahead, there are some exciting ideas in the community draft proposal of [XQuery 4](https://dev.saxonica.com/blog/mike/2020/11/14-qt40-proposal-comments.html), led by Michael Kay and developed through the [XML.com Slack workspace](https://www.xml.com/news/2020-04-slack-workspace-for-the-xml-community/)'s #xpath-ng channel and in [several](https://github.com/qt4cg/qtspecs) [repositories](https://github.com/expath/xpath-ng) on GitHub.) If you haven't joined the XML.com Slack workspace, please do! If you've attended Balisage, you'll know what I mean if I say, it's like Balisage, but all year long; if you haven't, then just know it's a great, vendor-neutral forum for asking questions and talking about XML and markup technologies.

One final note: Thanks to the Center of Digital Humanities Research at Texas A&M University for supporting the book!

> CoDHR is pleased to announce the publication of XQuery for Humanists, the newest release in its Coding for Humanists book series published by the Texas A&M University Press!—[@TAMU_CoDHR, May 27, 2020](https://twitter.com/TAMU_CoDHR/status/1243563378586591233)
