---
layout: post
title:  "Static Resources and Their Place in a Web App"
date:   2017-05-12 00:00:00 -0500
---

I have a theory, it's not popular I don't think, but I believe it's sound. It stems from my latest investigations on making web application more pleasant to develop, maintain and use by end users. Here it is:

What if we were to minimize client side templating logic and instead serve dynamically generated static files? I believe that by doing so would minimize page load times, decrease page complexities and make both developers and end users much happier.

Before I dive into the details, there are a number of similar iterations of this already in the wild. AirBNB does something called [Isomorphic Javascript](https://medium.com/airbnb-engineering/isomorphic-javascript-the-future-of-web-apps-10882b7a2ebc) where code is written once and it works on both the client and server. It actually has nothing to do with static resources but the concept that does resonate with my idea is the notion of code reuse. Not in the typical sense, but instead in the processing power sense. No need to process the same template over and over if we know how it’s going to be generated. So that being said, technically [Twitter does](https://blog.twitter.com/2012/improving-performance-on-twittercom) a fraction of what I propose. They render and serve the first page request, and only the content above the window fold, entirely from the server. Additional actions like scrolling, hovering, and posting messages, are then separately called and data is templated by the client. Among the many reasons they do this, one convenient one is that virtually all of their pages are identical, so their server-side templating is rather static. But, what if we gave Twitter users an ability to customize their page? Not just modify the CSS, like reddit provides for each subreddit to modify, but access to duplicating or filtering the main feed? That’s where [Jekyll](https://jekyllrb.com/) enters the picture because it basically does exactly that. It’s a templating engine that grabs templates, matches them to data and generates static resources. Then it’s just a matter of adding data files and Jekyll will template and generate a static resource. It’s one limitation is that it focuses on static blog-like web pages. So where does that leave us?

Ultimately, I propose an idea to dynamically generate static templates, which, when requested, will be fetched entirely rendered with the asked for dynamically generated content. All on initial page load. All to minimize an end-user’s page load times. All because we generate the templates once and reuse them.

Check out this (repo)[https://github.com/philip-pannenko/static-dynamic-forms]. If my ramblings made zero sense, maybe my code will make more sense.
