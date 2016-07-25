---
layout: post
title: Choropleth Maps With Leaflet
modified:
categories: notes
excerpt: create a choropleth map with R and Leaflet
tags: [R, choropleth, maps, Leaflet, JavaScript, GIS]
image:
  feature:
date: 2016-07-25T09:16:04-04:00
comments: true
share: false
---

[Choropleth maps](https://en.wikipedia.org/wiki/Choropleth_map) are useful in displaying data across geographic regions. In these plots, the scale is represented by color and typically contained in defined spatial boundaries. 

There are a number of ways to make choropleth visualizations in R, including the [**ggmap** package](http://rforpublichealth.blogspot.com/2015/10/mapping-with-ggplot-create-nice.html) and the [**choroplethr** package](https://cran.r-project.org/web/packages/choroplethr/index.html). 
Both of the methods above seem to work fine. But I recently started using Leaflet, which is a JavaScript mapping library that's bound to R with the Leaflet pakcage. 

A couple advantages to using Leaflet:

1. Interactivity (e.g. zooming, panning, popups on hover, etc.)
2. Diversity of ["provider tiles"](http://leaflet-extras.github.io/leaflet-providers/preview/index.html) (i.e. styles of maps)

Dropping geocoded points on map with Leaflet is easy enough. All you need to do is:

1. Create a basemap with `leaflet()`
2. Add a map "tile" (style) with `addTiles()`
3. Add marker(s) for according to the latitude and longitude of the point(s) with `addMarkers()`

**nb leaflet supports piping with magrittr**

{% highlight r %}
library(leaflet)
library(magrittr)

leaflet() %>%
	addTiles() %>%
	addMarkers(lat = -22.9754567, lon = -43.3923875)

{% endhighlight %}

But that's just a point on a map ... what about the choropleth?

To color regions that by a density or value of a given metric, you first need to define the geographic boundaries. As the [**leaflet** documentation points out](https://rstudio.github.io/leaflet/) the package can "Easily render Spatial objects from the sp package."

For this example, we'll use [data on reported Zika cases as released by the CDC on Github](https://github.com/cdcepi/zika) to look at prevalence of Zika in the United States. 

So to compare state to state in a choropleth map, we first need the geographic definitions of each state. These are [publicly available on census.gov as shape files](https://www.census.gov/geo/maps-data/data/tiger-cart-boundary.html). 

Once they are downloaded, the **rgdal** package can read the data in. 

{% highlight r %}
library(rgdal)

states <- readOGR(dsn = path.expand("cb_2015_us_state_20m"), layer = "cb_2015_us_state_20m")



{% endhighlight %}

The `sp` object created above ("states") stores its data in slots, which can be accessed with the "@" operator. You can also use the base `plot()` to visualize the boundaries to make sure they look correct.

{% highlight r %}

states@data

plot(states)

{% endhighlight %}

With the boundaries created, you can attach whatever data you'd like to map. Again in this case, we'll be using Zika incidence for a single week in the United States. The code below reads the data (stored as a CSV on Github) and then aggregates it by state and attaches it as a column to the `states@data` data frame.

{% highlight r %}
library(readr)
library(dplyr)
uszikaraw <- read_csv("https://raw.githubusercontent.com/cdcepi/zika/master/United_States/CDC_Report/data/CDC_Report-2016-06-29.csv")

uszika <-
    uszikaraw %>%
    filter(location_type == "state") %>%
    mutate(NAME = gsub("United_States-", "", location)) %>%
    group_by(NAME) %>%
    summarise(Zika.Cases = sum(value))

states@data <- left_join(states@data, uszika)

{% endhighlight %}

After the data have been prepared, onto the mapping ...

First create a palette ...

{% highlight r %}

pal <- colorNumeric(
    palette = "YlGnBu",
    domain = states@data$Zika.Cases
)

{% endhighlight %}
Then the "popup" text to appear on hover ...

{% highlight r %}

casecountpopup <- paste0("<strong>", states@data$NAME, "</strong>", "<br>", "Number of Cases: ", states@data$Zika.Cases)

{% endhighlight %}

Now the Leaflet map itself. 

**nb the data will be mapped as "polygons" instead of "markers" and will be colored by number of Zika cases**

{% highlight r %}

leaflet(data = states) %>%
    addProviderTiles("OpenStreetMap.BlackAndWhite") %>%
    addPolygons(fillColor = ~pal(Zika.Cases), 
        fillOpacity = 0.8, 
        color = "#BDBDC3", 
        weight = 1,
        popup = casecountpopup) %>%
    addLegend(position = "bottomleft",pal = pal, values = ~Zika.Cases, title = "<strong>USA Zika Cases</strong><br>(Week of 6/29/16)") %>%
    setView(lat = 38.0110306, lng = -110.4080342, zoom = 3)

{% endhighlight %}

The choropleth map produced by the code above is [available here](http://apps.bioconnector.virginia.edu/interactive/zika.Rmd).

The [**leaflet** documentation](https://rstudio.github.io/leaflet/) provides resources on how to further customize Leaflet maps. And [this tutorial](https://rpubs.com/walkerke/leaflet_choropleth) a similar workflow of how to create a Leaflet choropleth with R.