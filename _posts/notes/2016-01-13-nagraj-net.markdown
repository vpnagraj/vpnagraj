---
layout: post
title: nagraj.net
modified:
categories: notes
excerpt: custom domain for this github pages site
tags: [github pages, jekyll, google, domain]
image:
  feature:
date: 2016-01-13T13:09:19-05:00
share: false
---

Not sure why I've been putting it off but I finally added a custom domain for this site, which is hosted by [Github Pages](https://pages.github.com/). Once I settled on 'nagraj.net' I started looking for domain services. [Google Domains](https://domains.google.com/about/) had the name available at a reasonable price, and I found [really good documentation that spells out exactly how to configure a Github Pages site to use a domain purchased via Google Domains](http://www.curtismlarson.com/blog/2015/04/12/github-pages-google-domains/).

If and when I have to buy, register and configure a custom domain name for a Github Pages site again, I'll probably refer to the [really good documentation that spells out exactly how to configure a Github Pages site to use a domain purchased via Google Domains](http://www.curtismlarson.com/blog/2015/04/12/github-pages-google-domains/) ... and I'll know ahead of time that the process is fairly simple:

- Buy the domain (Google Domains)
- Create the Github Pages site (Github)
- Add a CNAME file with the custom domain to the site directory root (Github)
- Register the IP addresses for Github (Google Domains)
- Register the CNAME for the site repo (Google Domains)
- Wait for the DNS magic to happen (Wherever The DNS Magic Happens)
