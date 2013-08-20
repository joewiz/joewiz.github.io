---
published: true
layout: post
title: Jekyll Blogging in the Browser with prose.io
tags: 
  - writing
  - jekyll
---

In my continuing quest to find good ways to write and edit this Jekyll- and Github Pages-based blog (see my last post about [Octopage](http://joewiz.org/2013/08/18/mobile-blogging-with-jekyll/) on the iPhone), let's  turn to the browser.  

Imagine this scenario: You're sitting at a computer and want to write a new post or edit an existing one.  You could use GitHub's quite good web-based editor.  But it only takes a look at how GitHub [handles the job of previewing](https://github.com/joewiz/joewiz.github.io/blob/master/_posts/2013-08-18-mobile-blogging-with-jekyll.md) the [Markdown source](https://raw.github.com/joewiz/joewiz.github.io/master/_posts/2013-08-18-mobile-blogging-with-jekyll.md) of that same last post to understand that GitHub's Markdown parsing abilities aren't great with Jekyll's YAML metadata (which holds items like the post's title, date, etc.).  Github's editor succeeds at letting you edit and commit your changes directly to Github, but comes short when it comes to Jekyll Markdown files.

Enter [prose](http://prose.io), a highly polished and beautifully designed web-based GitHub- and Jekyll-aware text editor.  While you can grab the source and write an entire CMS around it (as prose.io developers [Development Seed](http://developmentseed.org/) did rather radically for https://www.healthcare.gov/), you can also use [the prose.io site](http://prose.io) itself to edit files in any of your GitHub repositories.  In fact, right off the bat when visiting the site, you're prompted to authenticate via GitHub's OAuth interface.  Once authenticated, you'll see your list of repositories and can select a file to edit or create a new file.  

As you would expect, Prose has a nice Markdown-aware editor and preview tool and lets you commit your changes directly to Github, but the Jekyll-aware functions lay dormant until you choose to edit a Jekyll post or create a new file in a Jekyll repository.  A new icon appears in the editor's sidebar: Raw Metadata.  By default this is a dumb text area with YAML syntax highlighting.  But it's actually quite configurable to display just those fields you want to see, like Title, Tags, etc.  When creating a new file, prose.io also prepopulates the filename field with the conventional Jekyll date prefix, yyyy-mm-dd.  A nice touch.

I first heard about prose.io on episode 54 of [The Web Ahead](http://5by5.tv/webahead/54) podcast.  I discussed the episode in [my first post](http://joewiz.org/2013/07/23/goodbye-tumblr-hello-github/) about Jekyll, and it's worth a listen to learn about Jekyll in general.  But the guests on this episode also happened to be Dave Cole and Young Han from Development Seed, the crew that created prose.io.  If you want to listen to this section of the episode, the discussion about prose.io starts at 45:20.  

Were I to start my site again, I'd probably base it off the prose.io [starter](https://github.com/prose/starter) project.  It includes prose-specific configuration - meaning you don't have to add this configuration after the fact.

One caveat about prose: If you have started a new file, you will lose your work if you click on the prose.io icon to browse the About link or hit the browser's back button.  Ask me how I know.  This is the second time I've typed this article in prose.io.  So be sure to click on the Save icon.  I've chimed in on an existing issue about this.