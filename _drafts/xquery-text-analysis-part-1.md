---
published: false
---

Bill Turkel is perhaps best known in digital humanities circles as the author of the essential resource, [The Programming Historian](http://programminghistorian.org/).[^1] Besides this, Bill has been writing an excellent series of blog posts aimed at getting digital humanists familiar with Linux as a tool for text analysis:

1. [Installing Debian Linux in a Virtual Machine](http://williamjturkel.net/2013/05/31/installing-debian-linux-in-a-vm/)
2. [Basic Text Analysis with Command Line Tools in Linux](http://williamjturkel.net/2013/06/15/basic-text-analysis-with-command-line-tools-in-linux/)
3. [Pattern Matching and Permuted Term Indexing with Command Line Tools in Linux](http://williamjturkel.net/2013/06/20/pattern-matching-and-permuted-term-indexing-with-command-line-tools-in-linux/)
4. [Batch Downloading and Building Simple Search Engines with Command Line Tools in Linux](http://williamjturkel.net/2013/06/25/batch-downloading-and-building-simple-search-engines-with-command-line-tools-in-linux/)
5. [Named Entity Recognition with Command Line Tools in Linux](http://williamjturkel.net/2013/06/30/named-entity-recognition-with-command-line-tools-in-linux/)
6. [Doing OCR Using Command Line Tools in Linux](http://williamjturkel.net/2013/07/06/doing-ocr-using-command-line-tools-in-linux/)

After reading each article, my instinct was to write a companion piece geared around eXist-db, my personal go-to tool for work on digital text analysis.  All but the sixth article are great candidates for eXist-db.[^2]  What makes eXist-db attractive for digital research is its core strength: querying and manipulating XML data. And it's also a platform for sharing your work with others: it comes with a web server and an application development environment.  Sharing the work you do with eXist-db is as simple as emailing a link.[^3]

So, taking Bill's articles as inspiration, this will begin that companion series of articles, introducing eXist-db as a tool for text analysis.  In this first entry, we'll install eXist-db.

## Installing eXist-db

Installing eXist-db is simple.  Just [download the installer](http://exist-db.org/), run it (double-clicking on the file once the download is complete), and follow the installer's prompts.  eXist-db.org's article on [Getting Started](http://exist-db.org/exist/apps/doc/quickstart.xml) does a great job leading you through these steps.  The Getting Started video on the homepage is also a great way to learn how to install the application ([Direct YouTube link](http://www.youtube.com/watch?v=xvMau2aHRDo#at=132)).

## Looking ahead

Next time, we'll upload text into eXist-db and perform basic text analysis.

[^1]: See the first edition at [http://niche-canada.org/member-projects/programming-historian/ch1.html](niche-canada.org).  Bill was also the author of the award winning [Digital History Hacks](http://digitalhistoryhacks.blogspot.com/) blog.

[^2]: eXist-db doesn't have an OCR module, and while I'm sure the eXist-db community is up to the task of creating one (as its rich array of extension modules demonstrates), it doesn't really have to do everything under the sun.  

[^3]: Bill is offering an entire class on Digital Research Methods, with lessons that follow the same progression as these articles. See [the syllabus](http://williamjturkel.net/teaching/history-9877a-digital-research-methods-fall-2013/). Those are some lucky students at the [University of Western Ontario](http://history.uwo.ca/People/Faculty/turkel.html).