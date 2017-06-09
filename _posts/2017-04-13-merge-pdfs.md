---
layout: post
title:  "Merge/concatenate PDF files"
date:   2017-04-13 13:06:00
comments: true
categories: [ubuntu]
tags: ubuntu
---

A simple note post to remember how to concatenate 2 PDF files in an extremely easy way.

Simply : `sudo apt-get install pdftk`, then use it with:
`pdftk file1.pdf file2.pdf cat output mergedfile.pdf`

{% include twitter_plug.html %}
