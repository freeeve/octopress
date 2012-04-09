---
layout: post
title: "Fedora 15 LXDE on Dell Mini 10v (broadcom wireless and bluetooth support)"
date: 2011-06-02 01:28
comments: true
categories: ["linux","fedora"]
---
So, I decided to finally get rid of the old Ubuntu that came with my Dell Mini 10v,
 which has been complaining about not being able to upgrade for about the last 18 months--I guess Dell took down the RPM servers they had put up for their release of Ubuntu.
Thanks, Dell. :(
<!--more-->
The rpmfusion packages need to be installed, and then the broadcom wl driver needs to be installed, through the following commands:
<pre style="white-space: pre-wrap; word-wrap: break-word;">yum localinstall --nogpgcheck http://download1.rpmfusion.org/free/fedora/rpmfusion-free-release-stable.noarch.rpm

yum localinstall --nogpgcheck http://download1.rpmfusion.org/nonfree/fedora/rpmfusion-nonfree-release-stable.noarch.rpm

yum install kmod-wl</pre>
This basically got me to the point where I could connect to wireless. Relatively painless.

However, I still wanted to be able to use my bluetooth mouse. I bought the bluetooth option for the mini, and a mouse to go with it, which has so far been an excellent choice. None of those horrible USB things sticking out the side.

In any case, I went ahead did some more research on how this was done. Initially I stumbled upon a page recommending hidd, so I installed the various packages to get hidd, including one called "bluez-compat" (which sounded a bit suspicious). I did a bit more research and found a recommendation on the Fedora 15 wiki to turn on the bluetooth and to install the blueman bluetooth manager. It seems to work great<del datetime="2011-07-07T01:26:24+00:00">, although it doesn't seem to start connecting until I log in, which isn't a big deal</del>. I tested going in and out of suspend, and rebooting, and it continues to work (amazingly).
<pre>yum install blueman</pre>
Reboot to be on the new kernel, if necessary.
<pre>chkconfig bluetooth on
service bluetooth start</pre>
Then, you can run the bluetooth manager, and it was fairly straightforward to add my mouse. Just remember that you need to "pair" and "trust" the mouse after you connect to it--otherwise it won't reconnect automatically.
