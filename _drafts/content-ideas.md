---
title: "Content ideas"
tags: [writing]
---

Google Analytics shows a clear pattern: the posts that get sustained search traffic are the ones solving a specific, named problem with XQuery or eXist-db. The personal/reflective posts drive very little organic search. The takeaway is to lean into concrete technical tutorials while occasionally publishing an update post to re-engage existing readers.

Below are ideas organized by theme, roughly in priority order.

---

## 1. XQuery language updates (high search value)

The language has moved significantly since 2015.

- **XQuery 4.0 preview** — Arrow operators (`=>`), fat-arrow lambdas, the new `fn` namespace, `declare namespace` shorthand. Show how idiomatic XQuery 4.0 looks vs. 3.1 with before/after examples.
- **`array:` and `map:` functions you actually use** — A practical guide to the higher-order functions added in 3.1 that aren't obvious from the spec.
- **Fold, for-each, filter: functional XQuery patterns** — Following up on "XQuery's missing third function", a full treatment of higher-order pipelines using real data.
- **String functions and regex in XQuery 3.1+** — `analyze-string`, `tokenize`, `replace` with flags, and how they replace what you used to do with XSLT.

---

## 2. eXist-db in 2025 (re-engagement for existing readers)

Lots happened between 2021 and now that long-time readers would want to hear about.

- **State of eXist-db 2025** — What changed in 6.x, community health, alternatives like BaseX and Fusion/db, and where eXist still excels.
- **eXist-db and Docker in practice** — The ecosystem now assumes containers; a practical walkthrough for DH developers who are still on bare-metal installs.
- **Testing XQuery with xspec** — Unit testing for XQuery is mature now; this is a gap in most tutorials.

---

## 3. XML and AI (new angle, high interest)

The intersection of the XML/TEI world with LLMs is a natural fit for this blog's audience.

- **Prompting LLMs to produce valid TEI** — What works, what doesn't, and how to validate output with eXist or oXygen. Practical lessons learned.
- **Using XQuery to post-process LLM output** — LLMs produce messy XML; here's how to clean, validate, and enrich it programmatically.
- **Named entity recognition → TEI markup pipeline** — Combine a Python NER tool (spaCy, Stanza) with XQuery transformation to annotate a corpus.

---

## 4. Digital humanities workflows (tutorial depth)

These tend to rank well because DH practitioners search for exactly this kind of practical guidance.

- **Publishing a TEI edition with Jekyll** — A walkthrough of using XSLT to transform TEI → HTML and integrating it with a GitHub Pages site.
- **Building a simple corpus search with eXist-db** — Full-text search, facets, and a minimal front-end. A natural successor to the `history.state.gov` work.
- **Updating "Filling PDF forms with XQuery"** — That post is old but widely read (PDFtk is still in use). A 2024-edition refresh with current tooling.
- **Airtable → XML roundtrip** — A writeup of the Airlock tool: when spreadsheets and XML need to talk to each other.

---

## 5. "XQuery for Humanists" follow-up

The book was published in 2020; a blog post tying it back to the current landscape would benefit both new and returning readers.

- **What I'd change in "XQuery for Humanists" today** — Honest reflection on what the book got right and what the language/tooling changes have made obsolete.
- **XQuery for Humanists: errata and updates** — A living-document post (or page) tracking corrections and linking to updated code examples.
- **Reading list: what to study after the book** — Pointers to specs, community resources, and companion tools that complement the book.

---

## 6. Short utility posts (quick wins, good for SEO long tail)

These are in the spirit of the "Trimming text" and "Chrome bookmarklet" posts — small, specific, easily discoverable.

- **Sorting mixed numeric/alphabetic strings in XQuery** — A common pain point with no obvious answer.
- **Deduplicating sequences in XQuery** — `fn:distinct-values` vs. map-based approaches and when each is better.
- **Converting between XQuery maps and XML** — Boilerplate everyone writes; a canonical reference version.
- **XSLT 4.0 for XQuery developers** — A crossover post for readers who know XQuery but haven't kept up with XSLT.

---

## Format suggestions

- Keep the code-heavy posts self-contained with runnable examples in eXist or BaseX.
- A "Fast-backwards"-style annual update post once a year is low effort and keeps the blog feeling alive.
- Consider a short "TIL" (Today I Learned) category for quick observations that don't warrant a full post.
