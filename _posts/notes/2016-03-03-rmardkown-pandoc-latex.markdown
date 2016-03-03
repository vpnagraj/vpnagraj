---
layout: post
title: LaTeX Symbols Inside Pandoc Table Column Headings Inside RMarkdown Documents
modified: 
categories: notes
excerpt: RMarkdown(Pandoc(LaTeX))
tags: [R, RMarkdown, LaTeX, Pandoc]
image:
  feature:
date: 2016-03-03T11:26:40-05:00
share: false
comments: true
---

In an effort to make a statisical report look needlessly complex (?) I just spent ~ 30 minutes figuring out how to include LaTeX symbols ... in a Pandoc table column header ... in an RMarkdown document. 

Check out the code and example:

```r
# set random seed
set.seed(123)

# set K for number of replications
K <- 1000

# rate (lambda)
lambda <- 3

# n 
n <- c(5, 10, 15, 20, 25)

# simulate data
sims <- lapply(n, function(x) replicate(K, rexp(x, lambda)))

# calculate sample means
sims_mean <- lapply(sims, function(x) apply(x, 2, mean))

# calculate sample variances
sims_var <- lapply(sims, function(x) apply(x, 2, var))


library(pander)
xbars <- sapply(sims_mean, mean)
vars <- sapply(sims_mean, var)
expected_s2 <- sapply(sims_var, mean)

propdata <- data.frame(n = n,
                       xbars = xbars, 
                       vars  = vars,
                       expected_s2 = expected_s2)

# assign names of dataframe with latex syntax 
names(propdata) <- c("n", "$\\mu_{\\bar{x}}$", "$\\sigma^2_{\\bar{x}}$", "$E(s^2)$")

pander(propdata)
```

|---
| n | $$ \mu_{\bar{x}} $$ | $$ \begin \sigma^2_{\bar{x}} \end $$ | $$ E(s^2)$$
|:-:|:-:|:-:|:-:
| 5 | 0.3362| 0.02264 | 0.1127 
|10 | 0.3358 | 0.01117 | 0.1114 
| 15 | 0.3325 | 0.007422 | 0.1095
| 20 | 0.3348  | 0.005182 | 0.1102
| 25 | 0.3294  | 0.004204 | 0.1082
|---

There are plenty of LaTeX cheat sheets like [this one](https://wch.github.io/latexsheet/latexsheet-0.png) to choose from. And there are plenty of examples of [using LaTeX in RMarkdown](http://www.calvin.edu/~rpruim/courses/m343/F12/RStudio/LatexExamples.html) as well. 

The thing is that the syntax for these LaTeX symbols gets trickier when run inside a chunk (i.e. block of R code). The code above demonstrate one way to get the pander table to output the column names with LaTeX syntax. 

The key line is:

`names(propdata) <- c("n", "$\\mu_{\\bar{x}}$", "$\\sigma^2_{\\bar{x}}$", "$E(s^2)$")`

That piece of code assigns names to the `propdata` object the desired syntax ... err ... sort of the desired syntax. You have to escape the `\` character that preceeds each of the references to `mu`, `bar` and `sigma`. 

It's not pretty but it works.

Maybe there's a better way to this with `pandoc.table()`?

