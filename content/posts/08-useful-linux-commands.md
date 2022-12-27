---
title: "Useful Linux Commands"
date: 2022-11-10
tags:
  - Linux
---

While Linux is great for servers and containers, it is a little bit a hassle to work with.
Especially when using the command line. Usually there a few different ways to do what you want and the
commands themselves are not very easy to remember. So here is a list of some useful commands. 
I will continue to update it when I stumble upon something worth adding.

`df -h`  
disk space usage

`lsblk -f`  
list hard drives and mount points

`mkfs.ext4 -L label /dev/sda1`  
create ext4 file system

`rsync -r /source/ dest`  
copy directory or sync changes

`iwconfig`  
configure a wirless network

`nmcli`  
Network Manager

`nmtui`  
text user interface for controlling Network Manager

`paste <(cat /sys/class/thermal/thermal_zone*/type) <(cat /sys/class/thermal/thermal_zone*/temp) | column -s $'\t' -t | sed 's/\(.\)..$/.\1°C/'`  
see system temperatures in human readable format

`stress-ng --cpu 4 --cpu-method matrixprod  --metrics-brief --perf -t 60`  
60 seconds cpu stress test on 4 cores

`iftop -BP -i eth0 -o 40s`  
view network traffic sorted by 40s average

