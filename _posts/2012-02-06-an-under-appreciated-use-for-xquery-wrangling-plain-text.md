---
published: true
layout: post
title: |
    An under-appreciated use for XQuery: wrangling plain text
tags: 
  - tei
  - xquery
  - exist
date: 2012-02-06 16:15:00-0400
---

In my experience teaching colleagues and students how to use [XQuery](http://en.wikipedia.org/wiki/XQuery) and [eXist-db](http://exist-db.org/) to create dynamic websites out of [TEI](http://www.tei-c.org/) documents, the "syllabus" usually starts in one of two forms. The first assumes that we already have well-formed TEI documents, and we can happily dive right into XML data structure manipulation with XPath and XQuery. The second starts with no XML: a PDF, a word document, or plain text. Now, of course, thanks to [OxGarage](http://www.oucs.ox.ac.uk/oxgarage/) and [oXygen](http://www.oxygenxml.com/demo/Smart_Paste_Copy_Paste_from_Web_Office_Documents_to_DITA_DocBook_TEI_XHTML_Documents.html), we have some good tools for deriving servicable TEI documents out of other formats. But more often than not, the text needs work. Sometimes, the importer fails to capture the structure implicit in the original. For all of these cases, XQuery proves to be an indispensable tool. XQuery's regular-expression functions (such as **matches**, **replace**, and **tokenize**), together with its excellent handling of sequences and recursive functions provide all of the tools one could ever need to tackle everything from simple to the most challenging text wrangling tasks.

Let's examine one challenging text wrangling scenario that XQuery makes quick work of: transforming an outline of subjects from a [Nixon Tapes subject log](http://nixon.archives.gov/forresearchers/find/tapes/finding_aids/tapesubjectlogs.php) into TEI (this one excerpted from [February 1971's Tape 47](http://nixon.archives.gov/forresearchers/find/tapes/finding_aids/february1971.php)):

    The President left at 8:48 am
        -Administration recommendations on Capitol Hill
        -Improvements
        -Richardson’s trip to New York
        -Health programs
                -Goals
                -Problems in present system
                -Approach
                -Emphasis on quality
        -Improvements in United States’ health care
                -Maternal deaths
                        -Rate
                        -Decline
                        -United States’ rate compared to other nations
                                -Reporting system
                -Data on health
                        -Differences in reporting system
                -Low-income people
                        -Whites
                        -Non-whites
                -Mortality rates
                        -Figures
        -Resource allocation
                -Rural areas
                        -Availability of care
                -Catastrophic care costs
                -Prevention
                -Problems

This plain-text outline of subjects discussed on tapes in the Nixon White House has a hierarchical structure that is clear to the human eye. But converting this text into a form of XML that captures this structure is challenging. Let's take a look at how we would represent this text in TEI:

{% highlight xml %}
<list>
    <item>The President left at 8:48 am
        <list>
            <item>Administration recommendations on Capitol Hill</item>
            <item>Improvements</item>
            <item>Richardson’s trip to New York</item>
            <item>Health programs
                <list>
                    <item>Goals</item>
                    <item>Problems in present system</item>
                    <item>Approach</item>
                    <item>Emphasis on quality</item>
                </list>
            </item>
            <item>Improvements in United States’ health care
                <list>
                    <item>Maternal deaths<list>
                            <item>Rate</item>
                            <item>Decline</item>
                            <item>United States’ rate compared to other nations
                                <list>
                                    <item>Reporting system</item>
                                </list>
                            </item>
                        </list>
                    </item>
                    <item>Data on health
                        <list>
                            <item>Differences in reporting system</item>
                        </list>
                    </item>
                    <item>Low-income people
                        <list>
                            <item>Whites</item>
                            <item>Non-whites</item>
                        </list>
                    </item>
                    <item>Mortality rates
                        <list>
                            <item>Figures</item>
                        </list>
                    </item>
                </list>
            </item>
            <item>Resource allocation
                <list>
                    <item>Rural areas
                        <list>
                            <item>Availability of care</item>
                        </list>
                    </item>
                    <item>Catastrophic care costs</item>
                    <item>Prevention</item>
                    <item>Problems</item>
                </list>
            </item>
        </list>
    </item>
</list>
{% endhighlight %}

One approach to this challenge would be to use find and replace. One of my colleagues tried this, using a tool at hand—Microsoft Word—and it required a 15-step set of find and replace routines, with manual corrections at many steps. Here is how we can use XQuery to accomplish the conversion a single step.  We will write a series of functions that perform the following transformations on the original text:

1. Take each line of text and turn it into a new XML element, ****, which captures the original indent level in an attribute (0 for no indent, 1 for a single tab, 2 for two tabs, and so on).
2. Place these **** elements into **** elements, and recursively nest the group elements according to the indent levels
3. Take the new group/line tree, transform it into a TEI list/item tree

Here is the first function, **text-to-lines**:

{% highlight xquery %}
declare function local:text-to-lines($text as xs:string) {
    let $lines := tokenize($text, '\n')
    for $line in $lines
    let $level := 
        if (matches($line, '^\s')) then 
            string-length(replace($line, '^(\s*).+$', '$1'))
        else 
            0
    let $content := replace($line, '^\s*(.+)$', '$1')
    return
        <line level="{$level}">{$content}</line>
};
{% endhighlight %}

This **text-to-lines** function uses the **tokenize** function to split the text file into a sequence of lines (\n is the new line character).  Then, for each line, we want to determine the "level" of indentation.  A line with 0 tabs is not indented, so we can assign it a level of "0"; for each tab, the level of indentation increases by one.  To check for the presence of tabs, we use the regular-expression-enhanced **matches** function: ^\s checks for a tab (or any whitespace space) at the beginning of the string.  If this "matches" test fails, we can assign a level value of 0.  If there are tabs, we need to isolate the tabs and count them.  We isolate the tabs with regular-expression-enhanced **replace** function.  Then we count the remaining characters (all tabs) with the **string-length** function.  We can't forget the text content of each line, so we use the **replace** function again to isolate the post-tab and post-hyphen content of each line.  Finally, we construct the new **** element.  

Passing our text to this function returns a new sequence of elements:

{% highlight xml %}
<line level="0">The President left at 8:48 am</line>
<line level="1">-Administration recommendations on Capitol Hill</line>
<line level="1">-Improvements</line>
<line level="1">-Richardson’s trip to New York</line>
<line level="1">-Health programs</line>
<line level="2">-Goals</line>
<line level="2">-Problems in present system</line>
<line level="2">-Approach</line>
<line level="2">-Emphasis on quality</line>
<line level="1">-Improvements in United States’ health care</line>
<line level="2">-Maternal deaths</line>
<line level="3">-Rate</line>
<line level="3">-Decline</line>
<line level="3">-United States’ rate compared to other nations</line>
<line level="4">-Reporting system</line>
<line level="2">-Data on health</line>
<line level="3">-Differences in reporting system</line>
<line level="2">-Low-income people</line>
<line level="3">-Whites</line>
<line level="3">-Non-whites</line>
<line level="2">-Mortality rates</line>
<line level="3">-Figures</line>
<line level="1">-Resource allocation</line>
<line level="2">-Rural areas</line>
<line level="3">-Availability of care</line>
<line level="2">-Catastrophic care costs</line>
<line level="2">-Prevention</line>
<line level="2">-Problems</line>
{% endhighlight %}

This XML structure now contains all of the information we need in order to take it from this "linear" form into a "nested" form. To do this, we start at the "outer"-most level and work our way "inward", grouping each level that contains inner levels together, and working recursively through until we reach the innermost items. Our group-lines function will take the sequence of line elements and group them together this way:

{% highlight xquery %}
declare function local:group-lines($lines as element(line)+) {
    let $first-line := $lines[1]
    let $level := $first-line/@level
    let $next-line-at-same-level := subsequence($lines, 2)[@level eq $level][1]
    let $group-of-lines-inside-this-level := 
        if ($next-line-at-same-level) then 
            subsequence(
                $lines, 
                1, 
                index-of($lines, $next-line-at-same-level) - 1
            )
        else 
            $lines
    return 
        (
        <group>{$group-of-lines-inside-this-level}</group>
        ,
        if ($next-line-at-same-level) then 
            local:group-lines(subsequence($lines, index-of($lines, $next-line-at-same-level)))
        else 
            ()
        )
};
{% endhighlight %}

This will process the first "level" of our lines into one or more group elements - in our case, it will result in a single group element, containing all of the original line elements. (If our source list had more than one 0-level line, this function would return as many group elements.) In effect, this function returns the outermost layer of our list.

To get the recursion started, we will pass this outer layer group element to our **process-groups** function, which in turn passes each group element to the **apply-levels** function:

{% highlight xquery %}
declare function local:process-groups($groups as element(group)+) {
    if (count($groups) gt 1) then
        <group>{
            for $group in $groups
            return
                local:apply-levels($group)
        }</group>
    else
        local:apply-levels($groups)
};

declare function local:apply-levels($group as element(group)) {
    <group>
        {$group/line[1]}
        {
        if ($group/line[2]) then 
            if (count(subsequence($group/line, 2)) gt 1) then 
                <group>{
                    for $group in local:group-lines(subsequence($group/line, 2))
                    return
                        local:apply-levels($group)                    
                }</group>
            else
                local:group-lines(subsequence($group/line, 2))
        else ()
        }
    </group>
};
{% endhighlight %}

The **apply-levels** function triggers the real recursive processing of the lines. It takes each group of lines, deposits the first line at the new level, and then runs the remaining lines in the group back through the **get-groups** function. This time, the **get-groups** function groups the inner lines according to their levels. This leaves us with a nicely nested set of group and line elements, with the original level attributes intact:

{% highlight xml %}
<group>
    <line level="0">The President left at 8:48 am</line>
    <group>
        <group>
            <line level="1">-Administration recommendations on Capitol Hill</line>
        </group>
        <group>
            <line level="1">-Improvements</line>
        </group>
        <group>
            <line level="1">-Richardson’s trip to New York</line>
        </group>
        <group>
            <line level="1">-Health programs</line>
            <group>
                <group>
                    <line level="2">-Goals</line>
                </group>
                <group>
                    <line level="2">-Problems in present system</line>
                </group>
                <group>
                    <line level="2">-Approach</line>
                </group>
                <group>
                    <line level="2">-Emphasis on quality</line>
                </group>
            </group>
        </group>
        <group>
            <line level="1">-Improvements in United States’ health care</line>
            <group>
                <group>
                    <line level="2">-Maternal deaths</line>
                    <group>
                        <group>
                            <line level="3">-Rate</line>
                        </group>
                        <group>
                            <line level="3">-Decline</line>
                        </group>
                        <group>
                            <line level="3">-United States’ rate compared to other nations</line>
                            <group>
                                <line level="4">-Reporting system</line>
                            </group>
                        </group>
                    </group>
                </group>
                <group>
                    <line level="2">-Data on health</line>
                    <group>
                        <line level="3">-Differences in reporting system</line>
                    </group>
                </group>
                <group>
                    <line level="2">-Low-income people</line>
                    <group>
                        <group>
                            <line level="3">-Whites</line>
                        </group>
                        <group>
                            <line level="3">-Non-whites</line>
                        </group>
                    </group>
                </group>
                <group>
                    <line level="2">-Mortality rates</line>
                    <group>
                        <line level="3">-Figures</line>
                    </group>
                </group>
            </group>
        </group>
        <group>
            <line level="1">-Resource allocation</line>
            <group>
                <group>
                    <line level="2">-Rural areas</line>
                    <group>
                        <line level="3">-Availability of care</line>
                    </group>
                </group>
                <group>
                    <line level="2">-Catastrophic care costs</line>
                </group>
                <group>
                    <line level="2">-Prevention</line>
                </group>
                <group>
                    <line level="2">-Problems</line>
                </group>
            </group>
        </group>
    </group>
</group>
{% endhighlight %}

As you can see, while this respects the original indentation levels of the source text, this is not yet proper TEI. Also, we have some seemingly redundant group elements. The final step is to whittle this structure down to proper a TEI list/item format. For this, we will write a **groups-to-list** function, which starts the new list element and calls a helper function, **inner-groups-to-list** for the remainder of the transformation:

{% highlight xquery %}
declare function local:groups-to-list($group as element(group)) {
    <list>{local:inner-groups-to-list($group)}</list>
};

declare function local:inner-groups-to-list($group as element(group)) {
    if ($group/line) then
        for $item in $group/line
        return
            <item>{
                $item/text()
                ,
                if ($item/following-sibling::group) then
                    <list>{local:inner-groups-to-list($item/following-sibling::group)}</list>
                else 
                    ()
            }</item>
    else (: if ($group[not(line)]) then :)
        for $g in $group/group 
        return 
            local:inner-groups-to-list($g)
};
{% endhighlight %}

Finally, we have a nice TEI list:

{% highlight xml %}
<list>
    <item>The President left at 8:48 am<list>
        <item>-Administration recommendations on Capitol Hill</item>
        <item>-Improvements</item>
        <item>-Richardson’s trip to New York</item>
        <item>-Health programs<list>
            <item>-Goals</item>
            <item>-Problems in present system</item>
            <item>-Approach</item>
            <item>-Emphasis on quality</item>
        </list>
        </item>
        <item>-Improvements in United States’ health care<list>
            <item>-Maternal deaths<list>
                <item>-Rate</item>
                <item>-Decline</item>
                <item>-United States’ rate compared to other nations<list>
                    <item>-Reporting system</item>
                </list>
                </item>
            </list>
            </item>
            <item>-Data on health<list>
                <item>-Differences in reporting system</item>
            </list>
            </item>
            <item>-Low-income people<list>
                <item>-Whites</item>
                <item>-Non-whites</item>
            </list>
            </item>
            <item>-Mortality rates<list>
                <item>-Figures</item>
            </list>
            </item>
        </list>
        </item>
        <item>-Resource allocation<list>
            <item>-Rural areas<list>
                <item>-Availability of care</item>
            </list>
            </item>
            <item>-Catastrophic care costs</item>
            <item>-Prevention</item>
            <item>-Problems</item>
        </list>
        </item>
    </list>
    </item>
</list>
{% endhighlight %}

We can run this entire transformation in one step:

{% highlight xquery %}
let $lines := local:text-to-lines($text)
let $groups := local:group-lines($lines)
let $processed-group := local:process-groups($groups)
let $list := local:groups-to-list($processed-group)
return
    $list
{% endhighlight %}

From this step, a transformation to HTML for presentation is trivial, and using this new structure to drive search applications is a matter of loading the final document into eXist-db.  We've taken our list from a flat text file and have given it structure, enabling a whole range of uses.

* * *

**Update:** This post was migrated from my old posterous.com blog in Dec. 2014, thanks to the [Wayback Machine's copy](https://web.archive.org/web/20130501233347/http://joewiz.posterous.com/an-under-appreciated-use-for-xquery-wrangling).

Also, for posterity, I've adapted a few comments I was able to retrieve:

- *Chris Wallace* (February 6, 2012): Hi Joe - nice example of the power of XQuery - I was reminded of the approach I used in the JSON-to-XML function I wrote so I wrote up my use of util:parse for this problem. (I still have problems putting code in Posterous - how do you do it?	

- *Aaron Marrs* (February 6, 2012): Fascinating stuff, Joe!

- *Joe Wicentowski* (February 7, 2012): Thanks for your comments, Chris and Aaron! Chris - Your idea to use util:parse() is great. Thanks for writing it up and posting code. It shows how XQuery, you can even use text to fold flat text, before bringing it into the XML dimension. (Indeed, working on this challenge and the post has got me thinking how bringing documents into XML is analogous to folding a one dimensional object - linear text - and bringing it into a world of two dimensions - nested nodes or concentric circles.) Everyone: Chris's alternative approach is at [http://kitwallace.posterous.com/converting-an-indented-list-to-a-tree-the-pow](http://kitwallace.posterous.com/converting-an-indented-list-to-a-tree-the-pow). Chris - to answer your question about how I posted the code, I used oXygen to escape the angle brackets in my XML and XQuery, and then pasted the code into Posterous's HTML view, surrounded by a pre and code set of tags. This was painful. I am thinking of looking into github gists - apparently posterous will automatically expand them. See [http://blog.posterous.com/posterous-now-supports-traileraddict-embeds-a](http://blog.posterous.com/posterous-now-supports-traileraddict-embeds-a).

