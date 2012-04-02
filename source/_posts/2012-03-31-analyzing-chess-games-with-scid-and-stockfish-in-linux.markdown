---
layout: post
title: "Analyzing chess games with Scid and Stockfish in Linux"
date: 2011-06-11 21:24
comments: true
categories: chess
---
Much of these instructions probably apply to Windows as well, but I’m writing this from a Linux perspective for now. I may do another guide for Windows.
<!--more-->
First, let me explain why I chose Stockfish over other engines. I have been using Crafty for analysis, but I recently decided to look at the market a bit to see if there were better options. In particular, I noticed on some game analyses that there were several “top move” options proposed (I’ve since learned this is a feature of the UCI standard), which is not something Crafty does. I wasn’t quite ready to jump into buying Rybka, so I held a tournament between Crafty and Stockfish. Stockfish won by several games, in both long and short games, but especially long games. I was surprised, honestly. Anyway, Stockfish seems like a strong choice for analysis, so here I am.

* Download Stockfish and unzip it
* Set the linux executables to be runnable:
```
chmod 700 stockfish-211-ja/Linux/*
```
* Download Scid
* In Fedora 15, I needed to install tk, tk-devel, and tcl-devel (as well as gcc and gcc-c++) to compile Scid
```
yum install tk tk-devel tcl-devel gcc gcc-c++
```
* After that run ./configure and make for Scid
* Run Scid (or add it to a menu): ./scid &
* Go to Tools->Analysis Engine and click New
* Fill out Stockfish for Name, the path to the linux stockfish executable for Command, and hit OK.  
{% img /images/scid-new-engine.png %}
* Select Tools->Analysis Engine again and select Stockfish. 
{% img /images/scid-select-engine.png %}
* An analysis window should appear on the right. You can select the number of top moves you want to see with the number spinner below that probably has 1 as default. I usually like to look at the top 5 moves. 
{% img /images/scid-analysis.png %}
This setup is perfect for my little $300 Dell Mini 10v, which runs on battery for several hours. It’s not a strong chess computer (being 32 bit doesn’t help), but it’s definitely strong enough for quick after-game analysis at tournaments.
