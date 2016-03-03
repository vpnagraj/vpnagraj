---
layout: post
title: Embedding Shiny Apps Inside RMarkdown Documents
modified:
categories: notes
excerpt: recap of how to make interactive rmarkdown documents
tags: [r, shiny, rmarkodwn, rmd, interactive]
image:
  feature:
comments: true
share: false
date: 2016-02-29T12:03:12-05:00
---

Embedding a Shiny app in an RMarkdown document is easy. 

So easy.

Caveman easy.

I'd [read about 'interactive' documents](http://shiny.rstudio.com/articles/interactive-docs.html) before, and may have even seen a presentation that used them at the [Shiny Developer Conference](http://www.gettinggeneticsdone.com/2016/02/shiny-developer-conference-2016-recap.html). Thought it was a cool idea but figured it was too complicated to put into practice.

Wrong.

All it takes is one extra line in the YAML front matter for the RMarkdown ...

`runtime: shiny`

... and that's pretty much it.

To host the document you do need a Shiny Server running on top of a version of R with the **rmarkdown** package installed. But besides that there's no additional server configuration necessary. The main thing to note is that you don't actually knit the document to HTML to host it. Just drop the .Rmd file in a subdirectory of your `shiny-server` root (i.e. where you keep all of the other apps) and the Shiny Server will take care of the rest.

Here's [a live example](http://apps.bioconnector.virginia.edu/interactive/moma.Rmd) and [the raw .Rmd](https://gist.githubusercontent.com/vpnagraj/943d12e12fd417f1845d/raw/9636528cee22357bda1ec73a455b3163050889cb/moma.Rmd) underneath it.