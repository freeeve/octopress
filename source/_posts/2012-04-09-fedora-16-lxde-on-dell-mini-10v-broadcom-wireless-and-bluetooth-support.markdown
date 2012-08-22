---
layout: post
title: "Fedora 16 LXDE on Dell Mini 10v (broadcom wireless and bluetooth support)"
date: 2012-04-09 21:55
comments: true
categories: ['fedora','linux', 'mini10v']
---
I confirmed that my post for Fedora 15 still works on Fedora 16:
<a href="/fedora-15-lxde-on-dell-mini-10v-broadcom-wireless-and-bluetooth-support/">
Fedora 15 LXDE on Dell Mini 10v (broadcom wireless and bluetooth support)
</a>

The only difference is that they've renamed the bluetooth service to "bluetooth.service", so the last step looks like this:

``` bash
chkconfig bluetooth.service on
service bluetooth.service start
```

It actually only gives you a warning, so the old commands work fine as well.
