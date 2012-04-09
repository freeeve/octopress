---
layout: post
title: "Migrating to Octopress"
date: 2012-04-01 12:41
comments: true
categories: ['octopress','blogging','mongopress']
---
So, after giving Mongopress a shot for a few months, I realized that if it's painful to make blog posts, you don't make them. <!--more-->
I thought Mongopress was a great idea, and I still think it is, but the current state of the project isn't usable enough. Nearly 6 months
later it's still in the same state (yes, I know it's open source, but to be honest I'm not a huge fan of PHP, so I didn't really want to dig into it).

In any case, I have been wanting to give octopress a try for a while. It says up front that it's more of a programmer's blogging tool, rather than
an everyday user. The reason for this is it requires a bit of framework installation, specific versions of Ruby, etc. I have a feeling that
non-programmer users might find a similar, but slightly easier to install tool very handy.

One of the cool parts of Octopress is it doesn't use a database! Why is that cool? Well, it's basically the equivalent of a cached installation of wordpress, but it's always cached, so it's very little work for the hosting server. It would be hard to bring a server to its knees just asking for files from the filesystem. You lose a bit of dynamicness with this, but you can push that to the client side if you really want to. And you can use web services (like those listing the latest tweets, githup repos, and comments, etc.). I was already using disqus for comments with mongopress, so it was a breeze to migrate my comments to octopress.

I was able to write a new liquid template filter for viewing chess games, in no time at all--it's basically only a few lines of code. So now I can just copy my pgn file to my images folder (I was too lazy to make a new folder), and then put something like:

{% gist 2279934 example %}

Directly into the post. That's pretty simple, I'd say. I meant to set up a similar thing in mongopress, but it wasn't nearly as trivial. Anyway, I think Octopress has a lot going for it. I'll comment more on the pros and cons later.

If you want to do something similar (octopress + chess game viewer), you can use my code at: https://github.com/wfreeman/octopress-chesstempo-viewer -- I'll post some installation instructions later.
