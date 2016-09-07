---
layout: post
title:  "Compact citations with elsevier.cls document class"
date:   2016-09-6 20:17:00
comments: true
categories: [latex]
tags: latex
---

In a `\documentclass{article}` Latex document, the `\usepackage{cite}` will automatically compact your citations from `[1, 2, 3, 4]` to `[1-4]` for example ([check here to learn more about it](http://texblog.org/2007/05/28/mulitple-reference-citation/)).

Now if you are using the `\documentclass{elsarticle}` to [send your manuscript to an Elsevier journal](https://www.elsevier.com/authors/author-schemas/latex-instructions), the previous solution does not work. The reason is that the `elsarticle.cls` class document is designed to work with `natbib` which uses the `cite` command and creates a conflict with `\usepackage{cite}`.
A solution to get your compacted citations to work without `\usepackage{cite}` is provided in [the elsarticle class documentation](https://www.elsevier.com/__data/assets/pdf_file/0008/56843/elsdoc-1.pdf), add the following line before the document's end:

```
\biboptions{sort&compress}
```

**PS: I tested everything with** `bibtex`.

{% include twitter_plug.html %}

