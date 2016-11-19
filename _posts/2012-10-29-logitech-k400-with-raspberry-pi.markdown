---
layout:     post
title:      "Logitech K400 and Raspberry Pi"
date:       2012-10-29 21:59:00 +0100
comments:   true
categories: [Hardware,IoT,Tips&Tricks]
tags:       [Raspberry Pi,OpenELEC,Raspbmc,XBian,XMBC]
---

Sometimes the [debian wheezy raspbian](http://www.raspberrypi.org/downloads) image, [Raspbmc](http://www.raspbmc.com) and [XBian](http://xbian.org) can't find my Logitech K400 keyboard. This tip should help.

- Put this lines in the `/etc/rc.local`
  
  ```bash
  sleep 5
  rmmod hid-logitech-dj
  modprobe hid-logitech-dj
  ```
  
- If you have german language problems with LXDE, then put this line in your `/etc/xdg/lxsession/LXDE/autostart`
  
  ```bash
  setxkbmap -layout "de,de"
  ```

Reboot the **RaspBi** and have fun.
