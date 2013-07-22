---
layout: post
title: XQuery's missing third function
tags:
- xquery
- search
- text mining
- dh
---
Even if you don't know XQuery, if you've only heard of it, you know XQuery is a language built for querying, or searching, XML. And if you've started learning XQuery for a digital humanities project, you use XQuery because it helps you _search the text_ that you've put inside your XML. Given your interest in searching text, it's likely that the first function you learn in an XQuery class or tutorial is `contains()`. Take this simple XPath expression:

{% highlight xquery %}
//book[contains(title, "arm")]
{% endhighlight %}

This economical little query searches through all the books in your XML for those with a title that contains the phrase "arm" — you know, all of your books about armadillo shwarma. Tasty, right?

Then in the next lesson you learn about the `matches()` function, which can do the same simple searches as `contains()` but can also handle patterns, expressed using a pattern matching language called regular expressions:

{% highlight xquery %}
//book[matches(title, "[A-Za-z]\d{2}T!")]
{% endhighlight %}

This finds titles like "W00T!" and "L33T!" — an upper or lower case letter, two digits, and a capital T, and an exclamation mark. Slick!

Then, naturally, you learn the `highlight-matches()` function, which turns highlights the phrase or pattern that you searched for:

{% highlight xquery %}
//book[highlight-matches(title, "[PT]ickl[a-z]+")]
{% endhighlight %}

This highlights the matching portion of the book titles: "The Art of **Pickling**" and "How to **Tickle** the Ivories like a Pro." Super!

…

But wait! The `highlight-matches()` function never appears in your lessons or class materials. It's not in the XQuery spec. Not the 1.0 version, not the 3.0 version. Surely, this must be a mistake. No, your teacher says. You [google for it](http://lmgtfy.com/?q=xquery+highlight+search+results). You click through the links. Stuff about indexes? Proprietary functions? The disappointment sets in. Really? No standard way to highlight the search results?

This was my experience, lasting several years, until today. I realized that I could combine two features of XQuery 3.0 — the `analyze-text()` function and higher order functions — to write a simple, implementation-independent `highlight-matches()` function, allowing us to write queries like this:

{% highlight xquery %}
local:highlight-matches(
    //book/title,
    "[PT]ickl[a-z]+",
    function($word) { <b>{$word}</b> }
    )
{% endhighlight %}

To make this easier to read, I've split the expression onto several lines. Here's what's going on:

1. This should look pretty familiar: we return all the pickling and tickling titles. But instead of applying `contains()` or `matches()`, we use `local:highlight-matches()`. And instead of putting the function inside a predicate (i.e., \[inside square brackets\]), we put the function outside. This is because our function doesn't merely serve as a condition (i.e., return all books whose title matches); it actually creates an _in-memory copy_ of the nodes that meet the condition with the highlight function applied.
2. Whereas we gave `contains()` and `matches()` two parameters (title and the phrase/pattern), we pass `local:highlight-matches()` a third parameter: a function. You may not have ever used a function as a parameter, but this is a perfectly valid thing to do in XQuery 3.0. It's the idea of "higher order functions" - or functions that can take other functions. The advantage of letting you define your own highlighting function is that you might not want to highlight with `<b>` tags. Rather, you might want to surround matches with a `<person key="#smith_michael">` tag. In other words, you might use `highlight-matches()` to do more than "highlight."
3. Submit the query. The `local:highlight-matches()` function finds the matching books and works its magic, returning the titles with the properly bolded phrases `<b>`Pickling`</b>` and `<b>`Tickle`</b>`.

…

But wait! If this `highlight-matches()` function isn't part of the XQuery specification, where can you get it?

I've posted the source code to `highlight-matches()` as [a GitHub gist](https://gist.github.com/joewiz/5937897). Copy and paste the code into any XQuery 3.0-compliant engine. Need one? Try [eXist-db](http://exist-db.org/), which has a handy online sandbox called [eXide](http://exist-db.org/exist/apps/eXide/index.html) that you can access from any web browser.

Once you get the sample code working, try writing your own highlighting function to return underlined text or text with a yellow background—or find instances of people whose names you know appear in the text and tag them using `<person>` or proper TEI.

And enjoy!

(And if you're one of the people who figured this out long ago, or as soon as XQuery 3.0 came along — which admittedly is still in draft form but whose higher order functions and `analyze-string()` function that made this possible have been in place for some time now — please take a look at the code and add some comments or submit a pull request. Let's ensure everyone learns this function right after `contains()` and `matches()`, okay?)