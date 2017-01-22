---
layout: post
title:  "Form Builder"
date:   2017-01-17 00:00:00 -0500
---

As a developer I'm often reminded to don't repeat yourself (DRY). One thing I've struggled to figure out how to do is to follow the DRY principle with code that's instincively hard to maintain (ie: run-time languages). This is why I decided to work on testing a potentially terrible idea. Not as terrible as [this](http://thedailywtf.com/articles/the-inner-json-effect) one though. Behold a form builder.

But wait, it's not just any form maker. No. That'd be pretty boring. Instead I wanted to try to fullfill the following criteria:
* create and maintain a dynamic object based on the provided configuration and form state
* render HTML elements based on a configuration json schema
* tie validation and rules between one-to-many elements

I *think* I [succeeded](https://philippannenko.github.io/form-builder/).

Ultimately, it demonstrates that using two configurations files, it's possible to use LEGO like components to create a form schema that can be generated. More impressively, it demonstrates it's possible to use predefined behaviors to perform cascading-like model transformations, validations and HTML element attributes changes.

