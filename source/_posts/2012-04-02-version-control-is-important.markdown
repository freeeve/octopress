---
layout: post
title: "version control is important"
date: 2010-10-08 02:15
comments: true
categories: ["programming", "version control"]
---
It's 2AM. Do you know what you changed in the code 3 months, 2 days, and 12 hours [or insert random amount of time here] ago?

Do you? It could be important.

If you use version control, you do.
<!--more-->

I see a lot of projects that are small, generally worked on by only one person, and... they don't have version control. Sometimes they just zip up the latest copy and save the zips of all of their "releases", which is at least a primitive form of version control--but I've seen where there are two copies of the code: what's on the developer's computer, and what's in production. I don't understand this phenomenon. These people seem like competent programmers, but they don't seem to know about the various [free] options for version control out there.

The latest invention has been distributed version control. I've used CVS and Subversion and even one called CMS (for VMS). These are similar in that they have a main repository that you need to connect to in order to get anything done. Subversion, of those three, seemed to handle things well, and a lot of people still use it for that reason. It has nice eclipse plugins and Tortoise for Windows explorer integration. But, what happens if you lose your connection to the repository one day? Or, what happens if you want to take your laptop on a trip and not have internet for a while, but want to perhaps commit some changes you've been working on? That's where distributed version control shines. Everyone that checks out the project has their own copy of the repository. That means you can operate in a disconnected manner as long as you want (you can even operate in a disconnected manner always, if you don't care about sharing your code)--although in the end you'll need to synchronize (and possibly merge) your changes into the main repository.

Distributed version control is available in the open source forms of: <a href="http://git-scm.com/">Git</a>, <a href="http://mercurial.selenic.com/">Mercurial</a>, and <a href="http://www.fossil-scm.org/">Fossil</a>. At least, these are the three I've tried extensively so far--I know there are several others. 

I listened to Linus Torvald's talk about git at google, and was instantly converted from Subversion to Git. I used it for several projects. My main complaint was that it was a bit flaky, especially working with large files over ssh, and the GUI support was pretty flaky as well. Merging was a bit of a pain, also. It's hard to sell tools to your team if they're hard to use or they fail randomly.

So, after moving to a new team, I decided to try out mercurial, which had the familiar Tortoise windows interface that had been nifty for Subversion. I was fairly happy with Mercurial, and used it for several projects. It was nice that the windows tools were familiar and focused on functionality--it was easy to sell to the team.

My current favorite is Fossil. I've been a follower of <a href="http://www.sqlite.org">sqlite</a> for a while--always amazed at how small they've gotten this [for all practical purposes] fully functional database. One day I noticed that D. Richard Hipp (the creator/maintainer of sqlite) migrated his repository for sqlite to a new version control system designed by him. The web interface was pretty slick--it had a primitive ticket (bug reporting) system and a wiki page built in, along with the repository files and history graph. I decided to give it a try at that point. It turns out it has several great features above the web interface. The most important I find in my work is the auto-sync. It greatly reduces the need to merge files between users. The main complaints I have at this point are the lack of a solid GUI for use while committing and updating the local repository. I generally just use the command line for all of that, and set up cgi-based repositories to auto-sync to/from. At this point I've got a dozen projects in Fossil on my server being hosted through the cgi method on apache.

Version control should be common sense. At the bare minimum it can be justified as a solid way of doing backups of your local code by copying the code to a remote repository. Aside from that, you know exactly what you've done, and when. You can track down bugs that are introduced far more effectively than you can without version control, or even with zip files of your releases. If you work with a team it should be a no brainer. It's so much easier to see what's been changed by someone when the lines in a file are annotated with that person's username.

That's probably enough for now. If you have a project without version control, give Fossil, Mercurial, or Git a try today! You should be able to get a local repository going in 20 minutes or less...
