---
layout: post
title: Installing R Packages From Source
modified:
categories: notes
excerpt:
tags: [R, packages]
image:
  feature:
date: 2016-03-23T12:11:02-04:00
share: false
comments: true
---


I upgraded to R version 3.2.2 several months ago and have pretty much reloaded all my packages.

But today I tried running a script that uses the [caret](https://cran.r-project.org/web/packages/caret/vignettes/caret.pdf) package's implementation of `glm()` for a logistic regression model. There was a problem ... **caret** wasn't installed. And when I ran `install.packages("caret")` I got this message:

> Error in loadNamespace(j <- i[[1L]], c(lib.loc, .libPaths()), versionCheck = vI[[j]]) : 
  there is no package called ‘pbkrtest’

I knew this was a dependency issue, and it was likely I'm not the only person who has run into it. And sure enough:

<http://stackoverflow.com/questions/34236629/unable-to-install-packagescaret-completely-in-r-version-3-2-3>

OK. So how do I install a package manually (i.e. from "source")?

I've had to do deal with this several times before, and have even had a consult devoted to this issue. But the answer is pretty simple:

<http://stackoverflow.com/questions/1474081/how-do-i-install-an-r-package-from-source>

As the upvoted response above indicates, the `install.packages()` function takes several arguments, inculding "repos" and "type". 

To install a package from source, go to the CRAN page of interest (in my case it's <https://cran.r-project.org/web/packages/pbkrtest/index.html>) and download the "Package source", which should have a "tar.gz" extension. Then in your R console you can use the following:

{% highlight r %}
install.packages(path_to_file, repos = NULL, type="source")
{% endhighlight %}

The `path_to_file` referred to above will depend on where you've downloaded the source, and whether or not you're using a Windows or Unix/Mac operating system.

On Windows the path is usually defined roughly as follows: "C:\\\Users/Downloads/pbkrtest_0.4-6.tar.gz"

And on Unix/Mac it will be roughly: "~/Downloads/pbkrtest_0.4-6.tar.gz"

I know all of this is detailed in the Stack Overflow post ... but it's something I've had to do several times now, so it's not a terrible exercise to duplicate here.

And incidentally, this didn't solve my problem ... **pbkrtest** wasn't loading with **caret** for a very good reason–it depends on R with a version of *at least* 3.2.3. 

Oops.

So I'll have to upgrade R ... or scratch the **caret** `glm()` for now, and just use the base **stats** version of logistic regression instead. Probably what I should have done from the start ...