---
published: true
layout: post
title: Converting Roman numerals with XQuery & XSLT I–IV
tags:
  - xquery
  - tei
  - dh
  - exist-db
date: 2021-05-30 11:45:00-0400
---

Have you ever needed a way to convert Roman numerals into Arabic numerals programmatically? One day in March 2015, I realized I did. I was writing a Schematron rule to check cross references like "see pp. 1–4" that take the form of a range of page numbers. The rule simply checked whether the pages in the range were ordered as expected. For example, a page range like "5–4" had to be flagged as an error, since 4 comes before 5. But to perform this check on cross references that used Roman numerals, such as "pp. i–v", I needed to find a way to convert Roman numerals into their Arabic numeral counterparts. For expediency's sake, I used automation guru Sal Mangano's `roman-to-number()` function ([_XSLT Cookbook_, 2nd Edition](https://www.oreilly.com/library/view/xslt-cookbook-2nd/0596009747/), pp. 70–72). Conveniently for my purposes, Sal used XSLT 2, which I was able to embed directly into my Schematron file. (If you're interested, see the commit where I [embedded Sal's function](https://github.com/HistoryAtState/frus/commit/9fb636dd13681807dc6f22ca42526d02519e6c01#diff-018e15eb2a6ad70244a86245c097cf6c7a5107e914750c1c258bbf51512f1e1eR219-R285) and [invoked it in my schematron](https://github.com/HistoryAtState/frus/commit/9fb636dd13681807dc6f22ca42526d02519e6c01#diff-018e15eb2a6ad70244a86245c097cf6c7a5107e914750c1c258bbf51512f1e1eR157-R159). Sal's function is an interesting read; it uses XML's guarantee of document order and the XPath `following-sibling::` axis to check if the next numeral is greater than the current numeral.)

Although Sal's XSLT function fit my immediate need, I was more at home with XQuery, so I was curious to find out if anyone had written an *XQuery* function for performing the same conversion. It's generally not difficult to port XSLT functions to XQuery (since both languages share the same XPath foundation), but sometimes XQuery lends itself to different approaches. Searching via Google, I found [one promising XQuery function](https://gist.github.com/mattio/7332cdbce988fe992a801141210a92bc), written by [Mattio Valentino](https://github.com/mattio) in [~2011](http://x-query.com/pipermail/talk/2011-May/003511.html). (Thanks to Mattio for [resurrecting it](https://twitter.com/MattioV/status/1398307362650394632) as I was writing this article!) I liked his version but noticed that it was written in XQuery 1.0—naturally, given the time period—and thought it might benefit from some features of XQuery 3.0, such as [inline functions](https://www.w3.org/TR/xquery-31/#id-inline-func), the [simple map operator](https://www.w3.org/TR/xquery-31/#id-map-operator), and the [switch expression](https://www.w3.org/TR/xquery-31/#id-switch). These features of XQuery 3.0 (which was still in draft form but had already been adopted by eXist-db) allowed me to reduce the number of functions from three to just one without sacrificing clarity. Problem solved and curiousity satisfied, I [posted my version to GitHub Gist](https://gist.github.com/joewiz/228e9cc174694e146cc8#file-roman-numerals-xqm) and moved on.

Last week, I learned about an implementation of [a Roman-to-Arabic numeral function written in Clojure](https://github.com/OpenRefine/OpenRefine/wiki/Recipes#convert-roman-numerals-to-arabic) from a post by [Thad Guidry](https://twitter.com/thadguidry) on the [OpenRefine mailing list](https://groups.google.com/g/openrefine/c/HdPwot5tYA0/m/pMy6sG9QAgAJ), and I was amazed that it required **only 6 lines!** In comparison, Sal's XSLT 2.0 function was 63 lines long, Mattio's XQuery 1.0 function was 40 lines (a handful fewer if you omit the comments), and my XQuery 3.0 version was 24 lines. Brevity doesn't equal superiority, but I wondered, did the Clojure version contain any insights that we could apply to simplify and streamline the earlier XQuery versions? 

While I don't know Clojure, I tried to pick apart the function's logic:

```clojure
(defn ro2ar [r]
  (->> (reverse (.toUpperCase r))
       (map {\M 1000 \D 500 \C 100 \L 50 \X 10 \V 5 \I 1})
       (partition-by identity)
       (map (partial apply +))
       (reduce #(if (< %1 %2) (+ %1 %2) (- %1 %2)))))
```

I found it somewhat terse, but I deduced that it achieves most of its compactness through [Clojure's syntax](https://clojuredocs.org/clojure.core/reduce) for performing the [fold](https://en.wikipedia.org/wiki/Fold_(higher-order_function)) function—a functional programming method for recursively processing a sequence of items and recombining the results to build up a return value. XQuery 3.0 had added a pair of functions for performing folds—[`fold-left()`](https://www.w3.org/TR/xpath-functions-31/#func-fold-left) and [`fold-right()`](https://www.w3.org/TR/xpath-functions-31/#func-fold-right)[^1]—but I hadn't taken advantage of them in my earlier adaptation of the Roman numeral function, because I didn't really understand them at the time. It wasn't until I worked on [_XQuery for Humanists_](https://xquery.forhumanists.org/) that I took a serious look at the fold functions.[^2] Folds are really powerful and worth learning—as you'll see below when we apply them to Roman numerals.

Besides folds, several other recent features of XQuery can simplify our function for Roman numeral processing. A large portion of the earlier XQuery functions were dedicated to spelling out how to convert individual Roman numeral symbols into integers: Sal used an XML lookup structure, Mattio used a conditional chain, and I had used a switch expression. The Clojure version used a map for this purpose, so I tried XQuery's [map](https://www.w3.org/TR/xquery-31/#id-maps) facility and found this nicely simplified the [lookup](https://www.w3.org/TR/xquery-31/#id-lookup) operation. I also found that XQuery's [array](https://www.w3.org/TR/xquery-31/#id-arrays) facility was a nice way to store and look up the tuple values for the fold function's accumulator, i.e., `[0, 0]` as the starting values and `[$running-total, $previous-number]` for ongoing values. Also, to achieve a similarly clean, linear flow as the Clojure version, I applied XQuery's [arrow operator](https://www.w3.org/TR/xquery-31/#id-arrow-operator) (`=>`), which allows us to elegantly pass values through a chain of functions. I also used XQuery's [`for-each()`](https://www.w3.org/TR/xpath-functions-31/#func-for-each) function to help when I applied inline functions in arrow operator chains. Lastly, while XQuery doesn't *yet* have a built-in function for splitting strings into characters, I was able to simplify the task of selecting each Roman numeral's individual characters by applying XQuery's powerful [`analyze-string()`](https://www.w3.org/TR/xpath-functions-31/#func-analyze-string) function.

Applying these techniques—folds, maps, the arrow operator, and the `for-each()` and `analyze-string()` functions—the new XQuery version weighs in at 25 lines, including liberal use of whitespace. It doesn't sacrifice clarity, assuming you understand how folding works. (Don't worry if you don't; I'll explain it below.) Here's the XQuery:

```xquery
xquery version "3.1";

module namespace r = "http://joewiz.org/ns/xquery/roman-numerals";

declare function r:decode-roman-numeral($roman-numeral as xs:string) as xs:integer {
    $roman-numeral
    => upper-case()
    => for-each( 
        function($roman-numeral-uppercase) { 
            analyze-string($roman-numeral-uppercase, ".")/fn:match/string()
        } 
    )
    => for-each( 
        function($symbol) { 
            map { "M": 1000, "D": 500, "C": 100, "L": 50, "X": 10, "V": 5, "I": 1 }?($symbol) 
        } 
    )
    => fold-right( [0, 0], 
        function($number as xs:integer, $accumulator as array(*)) { 
            let $running-total := $accumulator?1
            let $previous-number := $accumulator?2
            return
                if ($number lt $previous-number) then
                    [ $running-total - $number, $number ]
                else
                    [ $running-total + $number, $number ]
        }
    )
    => array:head()
};
```

This updated XQuery 3.1 function takes a string containing a Roman numeral and performs the following operations:

1. The Roman numeral is converted to uppercase.
1. The uppercase Roman numeral is split into individual symbols.
1. Each symbol is converted to its corresponding integer value. 
1. Moving through these integers from *right* to *left*, it compares each number to the previous value it had examined. (Upon first run, the starting "previous number" is simply 0.) If the current number is less than the previous number, the current number is subtracted from the running total, but otherwise, it is added to the running total. (Since the running total starts at 0, the first number is always added to 0, but any subsequent numbers will be added or subtracted accordingly.)
1. Once all numerals have been exhausted, the final total is returned.

For example, given the Roman numeral "vi", or 6:

1. The string is converted to the uppercase "VI".
1. The uppercase Roman numeral split into individual symbols: V and I.
1. These symbols are converted into two integers: 5 and 1.
1. Proceeding from right to left, the first number checked is 1. Since 1 is greater than the default "previous number" (0), 1 is added to the default "running total" (0), yielding a value of 1. 
1. Continuing from right to left, the next number checked is 5. Since 5 is greater than the previous number (1), 5 is added to the "running total" (1), yielding a value of 6. 
1. With all numerals exhausted, the function returns the final "running total": 6.

The `fold-right()` function is responsible for the logic starting with step 4. Starting with a default "running total" of 0 and a default "previous number" of 0 (these two default values comprise the function's `[0, 0]` array), it begins running through each of the integers from right to left, one at a time. After each comparison, it updates the `$accumulator` variable with the new pair of values for the "running total" and "previous number." Once it's finished, we can access the final total by looking up the first value in the array returned by the `fold-right()` function.

A more complex example is "xliv", or 44:

1. The string is converted to the uppercase "XLIV".
1. The uppercase Roman numeral is split into individual symbols: X, L, I, and V.
1. These symbols are converted into a sequence of 4 integers: 10, 50, 1, and 5.
1. Proceeding from right to left, the first number checked is 5. Since 5 is greater than the default "previous number" (0), 5 is added to the default "running total" (0), yielding a value of 5. 
1. Continuing from right to left, the next number checked is 1. Since 1 is less than the "previous number" (5), 1 is subtracted from the "running total" (5), yielding a value of 4. 
1. The next number checked is 50. Since 50 is greater than the "previous number" (1), 50 is added to the "running total" (4), yielding a value of 54. 
1. The final number checked is 10. Since 10 is less than the "previous number" (50), 10 is subtracted from the "running total" (54), yielding a value of 44. 
1. With all numerals exhausted, the function returns the final "running total": 44.

(Whereas [my earlier XQuery 3.0 version](https://gist.github.com/joewiz/228e9cc174694e146cc8#file-roman-numerals-xqm) raised an error when an invalid Roman numeral was included in the input string, this one just assigns any invalid symbols the value of 0.)

Looking ahead to [XPath and XQuery 4.0](https://github.com/qt4cg/qtspecs), a community effort being actively discussed in the [XML Community Slack](https://www.xml.com/news/2020-04-slack-workspace-for-the-xml-community/) (which just turned 1!), we'll be able to streamline our code even more with these features:

1. The new [`characters()` function](https://qt4cg.org/branch/master/xpath-functions-40/Overview-diff.html#func-characters) will let us jettison the inline function calling `analyze-string()`
1. The new, short form of the [inline function expression](https://qt4cg.org/branch/master/xquery-40/xpath-40-diff.html#id-inline-func) will let us jettison the verbose function expressions. 

Based on the draft spec, the XQuery 4.0 version will weigh in at 17 lines, using the same liberal whitespace:

```xquery
declare function r:decode-roman-numeral($roman-numeral as xs:string) as xs:integer {
    $roman-numeral
    => upper-case()
    => characters()
    => for-each(
        -> $symbol { 
            map { "M": 1000, "D": 500, "C": 100, "L": 50, "X": 10, "V": 5, "I": 1 }?($symbol) 
        }
    )
    => fold-right( [0, 0], 
        -> $number, $accumulator { 
            let $running-total := $accumulator?1
            let $previous-number := $accumulator?2
            return
                if ($number lt $previous-number) then
                    [ $running-total - $number, $number ]
                else
                    [ $running-total + $number, $number ]
        }
    )
    => array:head()
};
```

Applying a more compact whitespace policy similar to that of the Clojure version, we'll be able to not just meet it but beat it, with this **5 line** equivalent:

```xquery
declare function r:decode-roman-numeral($roman-numeral as xs:string) as xs:integer {
    $roman-numeral => upper-case() => characters() => fold-right([0,0], -> ($symbol, $accumulator) { 
        let $n := map { "M": 1000, "D": 500, "C": 100, "L": 50, "X": 10, "V": 5, "I": 1 }?($symbol)
        return if ($n lt $accumulator?2) then [ $accumulator?1 - $n, $n ] else [ $accumulator?1 + $n, $n ] } ) => array:head()
};
```

The purpose of such exercises, of course, is not to achieve the most compact or "best" implementation of this simple algorithm, but rather to illustrate how a simple challenge like converting Roman into Arabic numerals can be a gateway to powerful features built into XQuery, or your language of choice.[^3] The arrow operator, inline functions, and folds—all insights from functional programming—can both simplify and supercharge your XQuery. But, more generally, finding examples of how others have approached a challenge that you find interesting is a great way to learn, get things done, and—perhaps, before long—contribute your own take.

<hr/>

[^1]: XQuery 3.1 added a second pair of functions for performing folds with arrays instead of sequences: [`array:fold-left()`](https://www.w3.org/TR/xpath-functions-31/#func-array-fold-left) and [`array:fold-right()`](https://www.w3.org/TR/xpath-functions-31/#func-array-fold-right).

[^2]: In particular, see Chapter 8, "Thinking Functionally." If you don't have [the book](https://xquery.forhumanists.org), we posted [a code sample](https://xquery.forhumanists.org/code/195-pp173/) from our discussion of `fold-left()`. 

[^3]: [Rosetta Code](https://rosettacode.org), a "programming chrestomathy," offers a compilation of [many languages' approaches to Roman numeral conversion](https://rosettacode.org/wiki/Roman_numerals/Decode). The same site provides a list of [similar sites](https://rosettacode.org/wiki/Help:Similar_Sites). For a fun look at how to accomplish one feat in many languages, see [99 Bottles of Beer](https://www.99-bottles-of-beer.net), with over 1,500 languages and variations (including [two in XQuery](https://www.99-bottles-of-beer.net/language-xquery-993.html)).
