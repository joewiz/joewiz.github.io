---
published: true
layout: post
title: A Google Chrome bookmarklet for fixing URLs with DNS errors
tags: 
  - chrome
  - bookmarklet
date: 2014-12-16 16:15:00-0400
---

**Update (March 18, 2020):** As of sometime in 2017, Chrome [disabled the use of javascript](https://groups.google.com/a/chromium.org/forum/#!msg/blink-reviews/ob1_Xw5SWQM/nDhD5QLNAAAJ) on chrome error pages. The technique shown below no longer works.

[Bookmarklets](http://en.wikipedia.org/wiki/Bookmarklet) are great.  They're just like any bookmark 
you add to your web browser, except they contain a snippet of Javascript that runs when you click 
on it.  One of the first bookmarklets I used was for [Instapaper](https://www.instapaper.com/save); 
it passes the window's current URL to the services so it could save the page for you to read later. 

Today I had a burning need for a new bookmarklet.  I received an email with a link to a URL redirect service 
that was located behind a corporate firewall, inaccessible from the public internet.  The URL was something
like this:

[http://redirect.company.blah/?url=http://www.google.com/](http://redirect.company.blah/?url=http://www.google.com/)

If you try to open this link, your browser will naturally tell you that "This webpage is not available." 
Fixing the URL is easy enough: just chop off everything before the `?url=` bit.  But doing this over and over
struck me as a chore that a bookmarklet could take care of. I'd written a few convenience 
bookmarklets in the past to switch between local and remote versions of my website. The javascript in these 
bookmarklets looks something like this one, which replaces the server name in the window's URL with 
`localhost:8080`:

``` javascript
javascript:(function(){window.open('http://localhost:8080'+window.location.pathname);})();
```

With the corporate intranet URL, though, I ran right into a roadblock using this approach: 
`window.location.href` was returning `data:text/html,chromewebdata` instead of the URL in the location bar. 
The problem was that Chrome's DNS error page had usurped the window's `location`! It seemed that the 
original URL was nowhere to be found in the DOM in `window.*` where I was used to looking.

But then I noticed that the text of Chrome's error page contained the original URL. While View Source was of 
no help, I brought up Google's Developer Tools (right-click on a web page, and select Inspect Element) 
and dug into the actual source code of the error page. I found the full original URL in a Javascript 
variable accessible via `templateData.summary.failedUrl`. Typing this phrase into the Developer 
Console returned the original URL.

    templateData.summary.failedUrl

... returned:

    http://redirect.company.blah/?url=http://www.google.com/

Bingo! Now I just needed strip off everything before the `?url=` parameter. Regex to the rescue! 

    templateData.summary.failedUrl.replace(/.*\?url=/,'')

... returned: 

    http://www.google.com/

With this in hand, I was able to create my bookmarklet:

``` javascript
javascript:(function(){window.location=templateData.summary.failedUrl.replace(/.*\?url=/,'');})()
```

If you'd like to use this bookmarklet too, just drag the following link into your bookmark bar:

[de-redirect](javascript:(function(){window.location=templateData.summary.failedUrl.replace(/.*\?url=/,'');})())

(Note: Jekyll appears to be escaping some of the characters in this bookmarklet. It still works, but if 
you avoid %-encoded characters when possible as I do, just replace the bookmarklet's URL with the sample code above.)