---
layout: post
title: Calculating Geographic Distance With R
modified:
categories: notes
excerpt: a brief tutorial that introduces two methods for iteratively computing distance
tags: [geocoding, gis, google]
image:
  feature:
date: 2015-12-08T15:27:43-05:00
---

I've been working on an analysis that requires distance measurements as data points. The original dataset only had place names (as strings) so I had to do some geocoding before I could perform my distance calculations. I've used the *ggmap* package (... an R interface to the [Google Maps API](https://developers.google.com/maps/documentation/geocoding/intro?csw=1)) to do this kind of thing in the past, but I haven't documented that work before now. 

The workflow is simple enough:

1. Calculate the latitude and longitude
2. Pass those coordinates through distance calculation function

There are a few ways to approach this. 

## `mapdist()`

The *ggmap* package includes a function called `mapdist()` that computes the distance between two locations based on a "from" and a "to" argument. These parameters accept strings. What's nice about this is that the two steps described above are combined in one. The names are geocoded and distances between the locations are computed via the [Google API Distance Matrix](https://developers.google.com/maps/documentation/distance-matrix/intro?hl=en). The **mode** can be set to "walking", "bicycling" or "driving" and the **output** format configured to either "simple" (data frame) or "all" (list). But with its defaults, the function gives you easy access to the distance in miles (or meters or kilometers) and duration in minutes (or seconds or hours):

~~~ r
library(ggmap)

arena_dist <- mapdist(from = "Madison Square Garden New York, NY", 
					  to = "The Palace of Auburn Hills Auburn Hills, MI")

arena_dist$miles

arena_dist$minutes

~~~

The method above works well for measuring a single distance between two locations. But there are some cases (including the one that inspired this post) that require measurements across many combinations of places. The code below achieves this by passing `mapdist()` into `lapply()`:

~~~ r
library(ggmap)

# create vector of place names
places_names <- c("Museum of Modern Art New York, NY",
                "Smithsonian Museum of American Art Washington, DC",
                "Brooklyn Museum Brooklyn, NY",
                "Walker Art Center Minneapolis, MN",
                "Fralin Museum of Art Charlottesville, VA")

dist_list <- lapply(places_names, 
                    function(z) 
                        sapply(z, 
                               function(x) 
                                   mapdist(from=places_names, to=x)$miles)

dist_list
~~~

This is great ... *but* there's another *but*. The [Google Maps API limits usage of its web services](https://developers.google.com/maps/documentation/business/articles/usage_limits)) to 250 queries per day. So say you have a list of 100 locations, and you want to compute a matrix of distances between all of them: 

`100 x 100 = 10000 > 2500`

Bummer.

This is where a combination of the `geocode()` function from the *ggmap* package and the `gdist()` function from the *Imap* package is helpful.

## `geocode()` + `gdist()`

There's an easy workaround to the Google Maps API query limit: use the Google Maps API ... just make fewer queries.

`geocode()` calculates the latitude and longitude of an input location name (as a string) via the [Google Maps Geocoding API](https://developers.google.com/maps/documentation/geocoding/intro?csw=1). Because it's calculating the distance once for each individual location, the resulting query count for a list of 100 institutions is 100. And with latitude and longitude available, you can rely on other packages to calculate distance. `gdist()` from the *Imap* package computes the [geodesic distance](https://en.wikipedia.org/wiki/Geographical_distance) based on the latitude and longitude of the first location, and latitude and longitude of the second location. Because `gdist()` takes **lon.1**, **lat.1**, **lon.2** and **lat.2** the code can be a little bit messier. The example below geocodes the same place names as above, then computes all of the distances between them:

~~~ r
# create vector of place names
places_names <- c("Museum of Modern Art New York, NY",
                "Smithsonian Museum of American Art Washington, DC",
                "Brooklyn Museum Brooklyn, NY",
                "Walker Art Center Minneapolis, MN",
                "Fralin Museum of Art Charlottesville, VA")

# geocode 
places_lat <- geocode(places_names, source="google")$lat
places_lon <- geocode(places_names, source="google")$lon

places_df <- data.frame(names = places_names,
                        lat = places_lat,
                        lon = places_lon)

# calculate geodesic distance with gdist() from Imap package

library(Imap)

places_dist <- list()

for (i in 1:nrow(places_df)) {
    
    places_dist[[i]] <- gdist(lon.1 = places_df$lon[i], 
                              lat.1 = places_df$lat[i], 
                              lon.2 = places_df$lon, 
                              lat.2 = places_df$lat, 
                              units="miles")
    
}

~~~
**NB** As of writing this blog post the *ggmap* package had been recently updated to Version 2.5.2, which sets the default **source** for the `geocode()` function set to "dsk". I tried that method and received inaccurate latitude and longitude results. I would recommend using "source='google'" if you plan on using `geocode()` + `gdist()`

## Alternative Solutions

Below is a list of a few possible alternatives to calculating geographic distances:

1. [Enable pay-as-you-go billing for the Google Maps Distance Matrix API to expand query limit](https://developers.google.com/maps/documentation/distance-matrix/usage-limits)

2. [Use the *geosphere* package](https://cran.r-project.org/web/packages/geosphere/geosphere.pdf)(anybody else used this?)

3. [Write your own great circle earth distance function](http://www.r-bloggers.com/great-circle-distance-calculations-in-r/)
