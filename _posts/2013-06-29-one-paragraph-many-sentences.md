---
layout: post
title: One paragraph, many sentences
tags:
  - xquery
  - nlp
  - exist-db
---
Where does each sentence in this post start and end? Given some schooling and well punctuated text, our brains handle this task pretty easily, but it turns out that telling a computer how to split a text into sentences is a bit tricky. In modern English we have a general rule: sentences begin with a capitalized word and end with a period. But there are plenty of exceptions to account for in writing a program to isolate sentences: other words in the sentence might be capitalized, and abbreviations can contain and end with those periods, whether they’re at the end of a sentence or not.

For some time, I’ve wondered about how to find the start and end of sentences, but couldn’t ever devise an approach that worked. Then, recently, inspired by my friend [Josh’s](http://bottsywattsy.tumblr.com/) comment during [a course we were taking on XQuery](http://joewiz.tumblr.com/2013/04/27/reflections-on-learning-xquery.html) ("Could we use XQuery to pull out all topic sentences in a manuscript to help ensure the narrative flows logically and smoothly?"), I decided to return to the challenge. After some research I found a hint in [this post on stackoverflow](http://stackoverflow.com/questions/2103598/java-simple-sentence-parser/2103653#2103653), which unlocked a core insight: if you look at the individual words in the text, one at a time and in order, you can look for signs of a sentence break, and then apply logic against surrounding words and account for known exceptions to the rule, such as abbreviations or stock phrases. Proceeding this way through a text, you can isolate each sentence.

So, should you ever have the need for splitting text into sentences—perhaps for looking at all topic sentences in a chapter, or for counting sentences in a paragraph—check out [https://gist.github.com/joewiz/5889711](https://gist.github.com/joewiz/5889711). It’s a pair of XQuery functions for analyzing a chunk of text and identifying the sentences within. It’s a naive approach (see my notes at the top of that page), but it does a pretty good job with newspaper articles and other edited English prose.

It takes text like this paragraph (from [_FRUS_](http://history.state.gov/historicaldocuments/frus1964-68v06/d213)):

> 154613\. You should arrange to deliver following note to North Vietnamese Embassy. If in your opinion it can be done without creating an issue, we would prefer that you ask North Vietnamese Charge to come to your Embassy to receive note. “The U.S. Government agrees with the statement of the Government of the DRV, in its note of April 27, that it is necessary for Hanoi and Washington to engage in conversations promptly. The U.S. Government notes that the DRV has now agreed that representatives of the two countries should hold private discussions for the sole purpose of agreeing on a location and date. The U.S. Government notes that the DRV did not respond to its suggestion of April 23 that we meet for this limited purpose in a ‘capital not previously considered by either side.’ The U.S. Government suggested the DRV might wish to indicate three appropriate locations suitable for this limited purpose. The U.S. Government does not consider that the suggestion of Warsaw is responsive or acceptable. The U.S. Government is prepared for these limited discussions on April 30 or several days thereafter. The U.S. Government would welcome the prompt response of the DRV to this suggestion.”

and returns this:

> 1. 154613.
> 2. You should arrange to deliver following note to North Vietnamese Embassy.
> 3. If in your opinion it can be done without creating an issue, we would prefer that you ask North Vietnamese Charge to come to your Embassy to receive note.
> 4. “The U.S. Government agrees with the statement of the Government of the DRV, in its note of April 27, that it is necessary for Hanoi and Washington to engage in conversations promptly.
> 5. The U.S. Government notes that the DRV has now agreed that representatives of the two countries should hold private discussions for the sole purpose of agreeing on a location and date.
> 6. The U.S. Government notes that the DRV did not respond to its suggestion of April 23 that we meet for this limited purpose in a ‘capital not previously considered by either side.’
> 7. The U.S. Government suggested the DRV might wish to indicate three appropriate locations suitable for this limited purpose.
> 8. The U.S. Government does not consider that the suggestion of Warsaw is responsive or acceptable.
> 9. The U.S. Government is prepared for these limited discussions on April 30 or several days thereafter.
> 10. The U.S. Government would welcome the prompt response of the DRV to this suggestion.”

I chose this text because of the many capitalized words and abbreviations throughout and the variations in punctuation. I also tested against several New York Times and Boston Globe articles, a tricky portion from [Moby Dick](http://stackoverflow.com/questions/14095971/how-to-tweak-the-nltk-sentence-tokenizer) that threw off some other utilities, and some made up sentences with edge cases.

If you want to give it a try with your own text, you can copy [the entire gist](https://gist.github.com/joewiz/5889711/raw/tokenize-sentences.xq) and paste it into [eXide](http://exist-db.org/exist/apps/eXide/index.html), the XQuery sandbox for [eXist-db](http://exist-db.org/); click “Run" to see the results. (Should work with any XQuery implementation though.)

Thanks for the inspiration, Josh! And thanks to [Christine Schwartz](https://twitter.com/caschwartz/status/349245318249984000) ‏for reminding me that GitHub gists are a great place to throw things up—things that may not deserve a full blown repository of their own. But, since gists are repositories under the hood, pull requests are welcome. There’s surely room for improvement in this code.