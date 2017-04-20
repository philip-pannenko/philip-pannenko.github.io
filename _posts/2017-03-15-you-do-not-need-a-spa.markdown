---
layout: post
title:  "You Do Not Need a SPA"
date:   2017-03-15 00:00:00 -0500
---

Misunderstanding browsers and how they work are the main reasons why I feel like there has been an influx of bloated and buggy single page web applications lately. I would argue that these same web applications could have been cheaper to develop and easier to maintain by simply leveraging two simple things: browser caching and knowing web protocols.

Before you read too much into that, no I don't have any hard evidence that there have been bloated apps, it's just a gut feeling I get from roaming the net's forums; and two, there's incredible value in using 3rd party vendors to perform model-view binding,  establish convention over configurations practices and provide re-use with components. I'm absolutely  all for DRY and 'don't re-invent the wheel' principles which is exactly why I feel that knowing the environment that these apps are going to be used in is so important. 

What I suspect is the problem is that, developers get enamored with some new shiny tool and end up convincing their non-technical upper management to use it instead of using where a boring stack would have sufficed. 

With that said, there's a ton of info our there how to cache and browser behaviors. I found that these two gave me something to think about:
* [Google Developers - Caching](https://developers.google.com/web/fundamentals/performance/optimizing-content-efficiency/http-caching)
* [A Study of Tabbed Browsing Among Mozilla Firefox Users](http://dubroy.com/research/chi2010-a-study-of-tabbed-browsing.pdf) Warning: PDF

My take away from it all is that there are two patterns to follow
* URL / Cache Public: Prevent server hits in the first place. Have consistent URLs and use hashes instead of question marks for dynamic pages. Browsers will cache one file in the following cases, page.html#id=3 & page.html#id=4 whereas browsers will cache two separate pages in the following cases, page.html?id=3 & page.html?id=4. The following will also result in two separate cached files, page/id/3 & page/id/4.
* 304 - Not Modified: When you have to hit the server for a request, check to see if modifications were made. If none, a few bytes to indicate a 304 is significantly smaller and faster than an entire payload. 

I'm sure there' cache busting strategies that come with the territory (after all, it’s one of the [two most difficult](https://martinfowler.com/bliki/TwoHardThings.html) things in software engineering) but at this point I’m still in the profiling and benchmarking stage of proving to myself that boring tech isn’t dead. I’ll get to the more interesting stuff later.

