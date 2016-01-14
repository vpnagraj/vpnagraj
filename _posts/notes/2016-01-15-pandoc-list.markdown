---
layout: post
title: pandoc.list
modified:
categories: notes
excerpt: formatting a character vector as a list in rmarkdown
tags: [pander, rmarkdown]
image:
feature:
date: 2016-01-15T14:07:29-05:00
---

One of my recent tortures has been formatting RMarkdown reports. The [pander](https://cran.r-project.org/web/packages/pander/index.html) package gives you access to all the [Pandoc options](http://pandoc.org/README.html) you can handle. Seems like a big mountain to climb, but once you get started it's actually not too rough.

In particular, the `pandoc.[WHATEVER]()` functions from pander make it easy to render nicely formatted objects in RMarkdown. 

An example ... say you had the following (not so arbitrary) character vector:


{% highlight r %}
bowie <- c("Young Americans", "Station To Station", "Low", "Heroes", "Lodger", "Scary Monsters", "Let's Dance")
{% endhighlight %}

You could just print the object:


{% highlight r %}
bowie
{% endhighlight %}

{% highlight r %}
## [1] "Young Americans"    "Station To Station" "Low"               
## [4] "Heroes"             "Lodger"             "Scary Monsters"    
## [7] "Let's Dance"
{% endhighlight %}

Doesn't look great. Maybe try it with the generic `pander()` function around the name of the character vector:


{% highlight r %}
# install.packages("pander")
library(pander)
pander(bowie)
{% endhighlight %}

_Young Americans_, _Station To Station_, _Low_, _Heroes_, _Lodger_, _Scary Monsters_ and _Let's Dance_

Still not great, but until a couple days ago that's probably where I would have left it. 

Then I tab-completed into the `pandoc.[WHATEVER]()` functions and realized I was doing it wrong ... 

In this case if I want a list then I should go with `pandoc.list()`:


{% highlight r %}
pandoc.list(bowie)
{% endhighlight %}

{% highlight r %}
## 
## * Young Americans 
## * Station To Station 
## * Low 
## * Heroes 
## * Lodger 
## * Scary Monsters 
## * Let's Dance 
## 
## <!-- end of list -->
{% endhighlight %}

That looks more like a list ... at least as it would be printed in a console.

If you want to see it bulleted just wrap another `pander()` around it:


{% highlight r %}
pander(pandoc.list(bowie))
{% endhighlight %}


* Young Americans 
* Station To Station 
* Low 
* Heroes 
* Lodger 
* Scary Monsters 
* Let's Dance 

<!-- end of list -->

And if you want to see the vector ordered:


{% highlight r %}
pander(pandoc.list(bowie, style="ordered"))
{% endhighlight %}


1. Young Americans 
2. Station To Station 
3. Low 
4. Heroes 
5. Lodger 
6. Scary Monsters 
7. Let's Dance 

<!-- end of list -->

The best part about these functions is that they're documented inside R man pages (as opposed to only in the generic pandoc documentation) ... check it out:


{% highlight r %}
?pandoc.list
{% endhighlight %}


{% highlight r %}
sessionInfo()
{% endhighlight %}

{% highlight r %}
## R version 3.2.2 (2015-08-14)
## Platform: x86_64-apple-darwin13.4.0 (64-bit)
## Running under: OS X 10.11.1 (El Capitan)
## 
## locale:
## [1] en_US.UTF-8/en_US.UTF-8/en_US.UTF-8/C/en_US.UTF-8/en_US.UTF-8
## 
## attached base packages:
## [1] stats     graphics  grDevices utils     datasets  methods   base     
## 
## other attached packages:
## [1] pander_0.6.0
## 
## loaded via a namespace (and not attached):
##  [1] magrittr_1.5    tools_3.2.2     htmltools_0.2.6 yaml_2.1.13    
##  [5] Rcpp_0.12.2     stringi_1.0-1   rmarkdown_0.8.1 knitr_1.11     
##  [9] stringr_1.0.0   digest_0.6.8    evaluate_0.8
{% endhighlight %}
