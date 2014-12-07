---
layout: post
title: "logitech k400 with raspberry pi"
date: 2012-10-29 21:59
comments: true
categories: [raspberry pi, linux, debian, xbmc]
keywords: raspberry pi,linux,debian,xbmc,openelec,raspbmc,xbian,logitech,k400,language,sprache
description: working with logitech k400 and raspberry pi
---

sometimes the [debian wheezy raspbian](http://www.raspberrypi.org/downloads) image, [Raspbmc](http://www.raspbmc.com) and [XBian](http://xbian.org) don't find my logitech k400 keyboard.
if you have the same problem like me, then this little tipp would help you.

put this lines
{% codeblock lang:bash %}
sleep 5
rmmod hid-logitech-dj
modprobe hid-logitech-dj
{% endcodeblock %}
in your
{% codeblock lang:bash %}
/etc/rc.local
{% endcodeblock %}
if you have german language problems with LXDE, then put this line
{% codeblock lang:bash %}
setxkbmap -layout "de,de"
{% endcodeblock %}
in your
{% codeblock lang:bash %}
/etc/xdg/lxsession/LXDE/autostart
{% endcodeblock %}
then reboot your raspbi and have fun.

this works for me.