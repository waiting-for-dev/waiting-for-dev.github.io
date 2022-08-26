---
layout: post
title: "Read your newsbeuter feeds in epub format"
date: 2014-07-20 17:10:37 +0200
comments: true
categories: unix
published: false
---
I love [newsbeuter](http://www.newsbeuter.org/) CLI RSS reader. When I discovered it I saw that it was what I had been looking for quite a long time. Very quick and easy to navigate through the feeds, when I find one that seems interesting enough, I press the `o` key and I can read it in *full-colors mode* in my GUI web browser.

I also love [calibre recipes API](http://manual.calibre-ebook.com/news_recipe.html). After hours working in front of my computer screen, sometimes I don't want to read anything more in it and thanks to it I can convert any RSS feed to an epub.

However, I don't like to do things twice, so when I find a new interesting RSS I don't want to add it first to newsbeuter and later prepare a calibre recipe for it. So I thought it would be great to have a calibre recipe which feeds are taken directly from newsbeuter url files.

The following is a dynamic calibre recipe which feeds are taken from `~/.newsbeuter/urls`. It expects the lines in the urls file to be in the format:

    http://address_to_the_feed.com "~Name of the feed" tags

`"~"` special newsbeuter tag is intended to be the feed name. In my script it must be the first to appear after the url. If you don't like it, it is very easily modificable.

{% gist 96d88816ba6aff2af3f1 newsbeuter.recipe %}

Running `ebook-convert newsbeuter.recipe .epub` will generate an epub with all the feeds from newsbeuter urls file.

Furthermore, you can filter by tags *overusing* the `tags` cli option (which will introduce that tags in the meta information for the e-book generated, but in fact it is good).

    ebook-convert newsbeuter.recipe .epub --tags="english,newspaper"

The CSS is taken from [gutenweb](http://lamarciana.github.io/gutenweb/), a simple typographical stylesheet I developed to have out-of-the-box styles that should be comfortable to read. Even if it won't be perfect for all those feeds mark-up, it is quick and realistic about the amount of time I can spend tweaking a recipe style.

A disclosure. I never program in Python except for some calibre recipe. So, sure the script has a lot of things to improve. So, please, I would be happy for any feedback.

Last, the icing on the cake, with the following function in your `~/.bashrc` file you can do cool things like:

    epub english newspaper

to create the epub file with all the feeds with both `english` and `newspaper`tags. Of course, substitute in it the path to the `newsbeuter.recipe` file for the one you need.

{% gist 96d88816ba6aff2af3f1 newsbeuter.bash %}
