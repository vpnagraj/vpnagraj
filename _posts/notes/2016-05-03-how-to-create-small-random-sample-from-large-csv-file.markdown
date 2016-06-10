---
layout: post
title: How To Create Small Random Sample From Large CSV File
modified:
categories: notes
excerpt: Subsampling
tags: [sample, random, R, Python]
image:
  feature:
date: 2016-05-03T12:19:11-04:00
---

>__TL;DR__
> __Problem__: How do I generate a small random sample of large CSV to be read into R?
> __Solution__: [subsample](https://github.com/paulgb/subsample)

Staring at the stop sign in RStudio console window is a bummer. I've spent way too much time doing that while reading in large data sets. And of course there's the possibility of crashing the R session altogether ... sometimes simply exploring the data can be prohibitive.

One solution to this is to read the data in smaller chunks, and then combine the pieces in R. This isn't trivial. And it may not be worthwhile, especially if you just want to poke around.

What about just looking at a single one of those smaller chunks? 

That might be a better approach, particularly if this is purely exploratory. But the problem here is how to read in a representative sample. Any of the "read" functions (i.e. read.csv(), read.delim(), etc.) give you a max number of lines to read in. But the data could be sorted in a non-random way so that the first _n_ lines are biased. So what about generating a small random sample outside of R and then reading that in?

I've found several solutions to this problem on [Stack Overflow](http://stackoverflow.com/questions/22261082/load-a-small-random-sample-from-a-large-csv-file-into-r-data-frame), most of which involve some Perl or BASH scripting ... but there's an easier way ...

`subsample` is a command line tool built with Python. As long as you have `pip` installed, you can use the following:

{% highlight python%}

pip install subsample

{% endhighlight %}

The workflow is simple:

1. Identify the original CSV file to sample
2. Decide how many rows you want
3. Pipe that to a new file

Implemented in code:

{% highlight python%}

subsample -n 1000 purple.csv > purple_sampled.csv

{% endhighlight %}

Then to get that into R:

{% highlight r %}

rain <- read.csv("purple_sample.csv")

{% endhighlight %}

The [documentation](https://github.com/paulgb/subsample/blob/master/README.rst) details things like dealing with header rows and setting a random seed to recreate samples.