---
layout:     post
title:      "Watching DVDs on Raspberry Pi"
date:       2014-11-24 10:57:48 +0100
comments:   true
categories: [Hardware,IoT,Tips&Tricks]
tags:       [Raspberry Pi,OpenELEC,Raspbmc,XBian,XMBC]
---

If you want watching DVDs on your [Raspberry Pi](http://www.raspberrypi.org) with an installed [OpenELEC](http://openelec.tv), [Raspbmc](http://www.raspbmc.com) or [XBian](http://xbian.org) you must buy a [MPEG-2](http://www.raspberrypi.com) licence. But not afraid, it's only a minimal amount.

To get your licence you need the device's internal 16-digit serial number (not the number printed on your board).

So go to your favorite console and connect your Raspberry Pi through ssh (I show you the steps by using [Cmder](http://bliker.github.io/cmder), a Raspberry Pi B+ and an installed OpenELEC image).

```bash
λ ssh root@192.168.1.19                                    
root@192.168.1.19's password: XYZ                             

##############################################             
# OpenELEC - The living room PC for everyone #             
# ...... visit http://www.openelec.tv ...... #             
##############################################             
                                                           
OpenELEC (official) Version: 4.2.1                         

OpenELEC:~ # cat /proc/cpuinfo                             

processor       : 0                                        
model name      : ARMv6-compatible processor rev 7 (v6l)   
Features        : swp half thumb fastmult vfp edsp java tls
CPU implementer : 0x41                                     
CPU architecture: 7                                        
CPU variant     : 0x0                                      
CPU part        : 0xb76                                    
CPU revision    : 7                                        
                                                           
Hardware        : BCM2708                                  
Revision        : 0010                                     
Serial          : 00000000XXXXXXXX <-- the serial number                        

OpenELEC:~ #                                               
```
> Another way to get the serial number is: `SYSTEM`->`Systeminfo`->`Hardware`

Now you can buy your licence at [raspberrypi.com](http://www.raspberrypi.com) with this serial number and put the licence keys in the config.txt of your linux distribution.

To do this on `OpenELEC` (connected via ssh) follow these steps:

1. remount the `/flash` folder as read write

```bash
OpenELEC:~ # mount /flash -o remount,rw
```

2. now it's possible to edit the file with `nano` (a light weight console based text editor)

```bash
OpenELEC:~ # nano /flash/config.txt
```

3. scroll to the bottom of the file at replace the zero filled licences with your one and save the file by typing `CTRL-X` and typing `Y`.

```bash
################################################################################
# License keys to enable GPU hardware decoding for various codecs
# to obtain keys visit the shop at http://www.raspberrypi.com
################################################################################

# decode_MPG2=0x00000000
# decode_WVC1=0x00000000
# decode_DTS=0x00000000
# decode_DDP=0x00000000
```

4. now make sure the changes are written to your SD card

```bash
OpenELEC:~ # sync
```

5. finally remount the file system as read only and reboot your Raspberry Pi.

```bash
OpenELEC:~ # mount /flash -o remount,ro
OpenELEC:~ # reboot
```

Restart your raspi and have fun!

That's it!
