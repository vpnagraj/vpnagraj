---
layout: post
title: Mulitple RMarkdown Reports and Multiple Data Sets
modified:
categories: 
excerpt: how to create "templated" rmarkdown reports
tags: [RMarkdown, R, loops]
image:
  feature:
date: 2016-07-21T08:38:31-04:00
comments: true
share: false
---

Until recently I had yet to run into a scenario where I needed to use RMarkdown to produce a "templated" set of reports. That is, a group of PDFs or HTML files with common R code (and therefore common report features, like figures, tables, summary statistics, etc.) executed dynamically on different data sets. 

I encountered this problem when I was trying to automate a workflow for generating reports on publication data. The datasets are compiled individually for each department (Neurology, Pathology, etc.) but the final product (a PDF) features a common set of plots based on the input data set.

The *brute force* solution here would be to:

1. Open up RStudio
2. Write an RMarkdown report that reads in the first data set
3. Click "Knit PDF"
4. Point the `read_csv()` in the RMarkdown file to the second data set
5. Click "Knit PDF"
6. Repeat steps 4 & 5 until all reports have been generated

I could have potentially > 50 data sets. So the process above wasn't going to work for me.

Instead I started looking around for ways to loop through a list of files and generate multiple PDF reports. Each PDF would be based on data from each file.

Stack Overflow features posts that speak to this issue. One post describes a similar problem, but in terms of [**knitr** with Sweave](http://stackoverflow.com/questions/15396755/using-loops-with-knitr-to-produce-multiple-pdf-reports-need-a-little-help-to). Another seems more relavent to my workflow (since I'm using RMarkdown rather than Sweave) but explains [how to create multiple reports from a *single* data set](http://stackoverflow.com/questions/26304576/how-to-best-generate-multiple-html-files-from-rmarkdown-based-on-one-dataset). 

My solution borrows from the posts above. The basic premise here is that you can use a single R script to "drive" the rendering of your RMarkdown file. [Another piece of documentation](http://brooksandrew.github.io/simpleblog/articles/render-reports-directly-from-R-scripts/) I found does a great job detailing this idea.

With everything above in mind, here's the workflow that works for me:

1. Move all data files to a "data" subdirectory of my project root
2. Check that data is prepared correctly (i.e. all columns are named the same, missing values are represented consistently)
2. Write an RMarkdown file (first code block below) that is generic enough to work for each data set 
3. Write an R script (second code block below) that:
	1. Loops through the data folder
	2. Creates an individual object (`pubs`) based on the given file in the loop 
	3. Renders a PDF (using `rmarkdown::render()`) that is named accodring to the raw data file.
4. Run the R script

{% highlight r %}
library(knitr)
library(readr)
library(dplyr)

filelist <- list.files("data")

somdat <- read_csv("All_SOM13-15v2_clean.csv")

# loop through the file list to read in data and clean it up

for (file in filelist) {
    
    fp <- paste("data/", file, sep="")
    
    # read in pubs but exclude web of science and xref
    pubs <- read_csv(fp) %>%
        filter(!grepl("WOS", Proprietary.ID) & !grepl("\\.", Proprietary.ID))
    
    rmarkdown::render(input = "pmbrochure.Rmd", 
              output_format = "pdf_document",
              output_file = paste(file, ".pdf", sep=''),
              output_dir = "reports")
    
}
{% endhighlight %}