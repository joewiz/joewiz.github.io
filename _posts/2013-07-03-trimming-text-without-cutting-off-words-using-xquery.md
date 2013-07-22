---
layout: post
title: Trimming text without cutting off words, using XQuery
tags:
- XQuery
link: https://gist.github.com/joewiz/5923408
---
A new Github gist:

> Helps trim phrases of arbitrary length to a maximum length, without cutting off words, as the substring() function would inevitably do, or ending on unwanted words

I wrote this to handle Tumblr photo posts, which have no explicit title, only a caption, and in the cases I've seen, "captions" are actually full-length posts.  I needed to trim these captions to a maximum length - e.g., 140 characters - without cutting off words or ending on unwanted words - e.g., the.