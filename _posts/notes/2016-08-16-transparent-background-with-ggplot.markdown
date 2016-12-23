---
layout: post
title: Transparent Plot Backgrounds
modified:
categories: notes
excerpt: transparency in ggplot2 plots
tags: [ggplot2]
image:
  feature:
date: 2016-08-16T13:27:57-04:00
comments: true
share: false
---

While I was preparing a figure for a research poster recently, I ran into an issue: my poster had an off-white background but the figure had a white background.

The first thing that came to mind was making the plot background transparent. Should be easy enough?

[It is.](http://stackoverflow.com/questions/7455046/how-to-make-graphics-with-transparent-background-in-r-using-ggplot2)

The gist is that you need to set the both panel and *and* plot backgrounds to transparent. It's probably a good idea to "turn off" the grid elements since otherwise they will default to a color. All of the above can be done with options via the `theme()` function.

And when saving the plot with `ggsave()` you also should indicate that the background is transparent.

{% highlight r %}

library(ggplot2)
data("airquality")

p <- 
    ggplot(airquality, aes(Solar.R, Temp)) +
    geom_point() +
    geom_smooth() +
    # set transparency
    theme(
        panel.grid.major = element_blank(), 
        panel.grid.minor = element_blank(),
        panel.background = element_rect(fill = "transparent",colour = NA),
        plot.background = element_rect(fill = "transparent",colour = NA)
        )

ggsave("airquality.png", p, bg = "transparent")

{% endhighlight %} 
