---
layout: post
title: "Analyzing chess games with Scid and Stockfish on Mac OSX Lion"
date: 2011-12-11 20:51
comments: true
categories: [chess, stockfish]
---
I recently acquired a macbook pro, and have been thoroughly enjoying it in all aspects. I thought I would give a quick update to my previous Scid and Stockfish in Fedora post, to show how the mac installation goes.

* Download Scid from http://scid.sourceforge.net/download.html
* Download Stockfish from http://www.stockfishchess.com/download/
<!--more-->
* As is fairly common with Mac OSX, Scid is a simple drag-to-Applications install.  
* Stockfish needs to be unzipped and moved to a known location. I copied the 64-bit executable to /Applications/ 
* Run Scid (it should be in your Applications), and go to the Tools->Analysis Engine menu item. 
{% img /images/scid1.png %}
* Click New in the Analysis Engine window.  
{% img /images/scid2.png %}
* Configure Stockfish by entering the name Stockfish, and selecting the executable (which I copied to Applications).  
{% img /images/scid3.png %}
* Click OK, and select Stockfish as your engine. It should begin to analyze the current position. I usually crank up the number of top variations to 5.  
{% img /images/scid4.png %}
