---
layout: post
title: A day of Jekyll housekeeping with Claude
tags:
  - jekyll
  - github-pages
  - claude
draft: true
---

I've been meaning to do some housekeeping on this blog for a while. The setup was showing its age: an old dependency manager, a broken analytics integration, a footer still linking to Twitter. Today I finally tackled it, with a lot of help from Claude Code.

Here's what we did.

## Modernizing dependencies and local dev setup

The blog had been using the `github-pages` meta-gem to manage its Ruby dependencies. This gem exists to mirror GitHub's server environment exactly, which made sense years ago when GitHub Pages ran your build for you — but it pins Jekyll to an old version and drags in roughly 60 transitive dependencies. Since GitHub Pages now supports custom GitHub Actions workflows, there's no reason to stay in that straitjacket.

Claude replaced `github-pages` with a direct dependency on Jekyll 4.4, shedding most of those pinned gems. Along with that came a few other things I'd been meaning to add:

- A `.ruby-version` file so rbenv or chruby picks the right Ruby automatically
- A GitHub Actions workflow that builds with Jekyll 4 and deploys to Pages via the modern upload/deploy approach
- A `.devcontainer/devcontainer.json` for a reproducible local dev environment — `bundle install` runs automatically on container creation and port 4000 is forwarded to the browser
- A `dependabot.yml` for monthly grouped updates of gems and Actions

A small follow-up: I added `host: 0.0.0.0` to `_config.yml` so `jekyll serve` is actually reachable through the devcontainer's port forwarding. (Obvious in retrospect.)

## Fixing Google Analytics (broken since July 2023)

The blog's `_config.yml` had a `UA-` tracking ID — Universal Analytics — which Google shut down in July 2023. The site had been sending analytics nowhere for over two years.

The fix was twofold: update to the GA4 measurement ID, and override Minima's built-in analytics snippet, which uses the legacy `analytics.js` API that GA4 doesn't understand. The replacement uses `gtag.js` and preserves the Do Not Track check from the original template.

## Switching from Twitter to Bluesky in the footer

The footer had been linking to a Twitter profile. Minima 2.5.x has no built-in Bluesky support, so Claude added a `_includes/social.html` override that swaps the Twitter entry for Bluesky, with the butterfly logo inlined directly (the Minima icon sprite doesn't include it), leaving everything else untouched.

## Upgrading to Ruby 3.4

Three places independently hardcoded Ruby 3.3: `.ruby-version`, `devcontainer.json`, and the workflow file. Claude bumped all three to Ruby 3.4, and updated the workflow to read from `.ruby-version` directly so they can't drift out of sync again. Ruby 3.3's standard maintenance ends in March 2026 anyway.

While in the Gemfile.lock, Claude also trimmed the platform list from 19 entries down to 5. The lock file had accumulated Android, RISC-V, musl/Alpine, and 32-bit x86 platform variants — none of which have any plausible connection to a Jekyll blog's development or deployment environments.

## Current state

The blog now runs Jekyll 4.4.1 on Ruby 3.4, deployed via GitHub Actions, with a lean Gemfile.lock, live GA4 analytics, Bluesky in the footer, and a devcontainer ready to go. The diff from all this: about 400 lines removed, 150 added.

Not bad for a Saturday.
