---
layout:     post
title:      "Set the default gcc version on a Raspberry Pi"
date:       2012-10-28 20:08:00 +0100
comments:   true
categories: [Hardware,IoT,Tips&Tricks]
tags:       [Raspberry Pi,OpenELEC,Raspbmc,XBian,XMBC]
---

My first experience with my **Raspberry Pi** was to compile some stuff. I needed an older gcc version than was pre-installed on **wheezy**. Installation wasn't the problem, the problem was to use the right gcc version. After searching the web I found a helper script created by [Jeff Carr-3](http://debian.2.n7.nabble.com/template/NamlServlet.jtp?macro=user_nodes&user=100722).

#### Script

```bash
#!/bin/bash 

usage() { 
        echo 
        echo Sets the default version of gcc, g++, etc 
        echo Usage: 
        echo 
        echo "    gcc-set-default-version <VERSION>" 
        echo 
        exit 
} 

cd /usr/bin 

if [ -z $1 ] ; then 
        usage; 
fi 

set_default() { 
        if [ -e "$1-$2" ] ; then 
                echo $1-$2 is now the default 
                ln -sf $1-$2 $1 
        else 
                echo $1-$2 is not installed 
        fi 
} 

for i in gcc cpp g++ gcov gccbug ; do 
        set_default $i $1 
done
```

#### Usage

```bash
sudo sh gcc-set-default-version 4.7
```
