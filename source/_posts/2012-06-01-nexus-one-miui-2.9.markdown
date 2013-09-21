---
title: Install Jelly Bean MIUI 2.9.29 in Nexus One using blackrose
date: 2012-06-01
category: android
tags: android, miui, jelly bean, blackrose, partition
lang: en
---

I tried Ice Cream Sandwich on my Nexus One before summer, and despite its quirks and unstable behaviour
from the early version I had (MIUI 2.5), I felt I couldn't go back to Android 2.3 after getting
used to it for a while.

When I tried to get a more stable ROM version (MIUI 2.9), I found lots of trouble, obscure fails
aggravated by a considerable lack of documentation about how to go for it. So here is how I made
it.

## Introduction

Android 4.1 Jelly Bean needs more space in the `/system` and `/cache` partitions than it's
originally allowed in the Nexus, so we'll need a custom bootloader and resize those partitions
before think about flashing the ROM itself.

Steps are:

1. Download Blackrose
1. Get fastboot to work
1. Install Blackrose and resize partitions
1. Install the ROM

## Download HBOOT Blackrose

Download and unzip [Blackrose](blackrose_120421.zip) and [Blackrose Manual](blackrose_manual_120421.zip) (check md5 checksums)

	$ md5sum blackrose_120421.zip
	f89de99e616a56e1bae29a9f8a190ee1  blackrose_120421.zip
	$ unzip blackrose_120421.zip
    $ ls blackrose_120421
	binary  BlackRose  BlackRose.exe fastboot-linux-i386  fastboot-linux-i386.zip  other

	$ md5sum oblackrose_manual_120421.zip
	3c2bc61256aba2df1575ce2b6c66c977  blackrose_manual_120421.zip
	$ unzip blackrose_manual_120421.zip
	$ ls blackrose_manual_120421
	fastboot-l  fastboot-m  fastboot-w.exe  go2.lol  go33.lol  go35.lol  hboot_220-16-200.nb0
	hboot_blackrose.nb0  README.txt

## Get fastboot to work

You'll need fastboot working properly in order to be able to interact with your phone.
The blackrose_manual version does include a fastboot implementation. 
We'll use `fastboot-l` for Linux.

1. Get your phone into `fastboot` mode. You should read `fastboot` on your phone's screen.
To enter into fastboot mode:
	1. Turn your phone off
	2. Turn your phone on while pressing the direction ball
	3. Enter into `fastboot` mode
1. Connect your phone via usb to the computer. You should read `fastboot USB` on your phone's
   screen.
1. Check wether it's detected by fastboot:
		$ ./fastboot-l devices
		HT066P800146fastboot
	If that does not work:
		1. Try with `sudo ./fastboot-l devices` (might be mounted with root permissions, you can [write a udev rule](http://wiki.cyanogenmod.com/wiki/Udev) to prevent this)
		2. Check your [fastboot setup](http://wiki.cyanogenmod.com/wiki/Fastboot), your [android sdk installation](http://wiki.cyanogenmod.com/wiki/ADB) and make sure that the  `android_sdk/tools` dir is within `$PATH`.

## Install Custom Blackrose and resize

Once you get `fastboot` to work, run the `Blackrose` executable which comes with
blackrose_120421, and install the `Custom` version, to resize the partitions on the same operation.

Mind the `skip` parameter, [it's important](http://forum.xda-developers.com/showthread.php?t=1842419).

	$ ./BlackRose skip
		Loading...
		* daemon not running. starting it now *
		* daemon started successfully *

		-------------------------------
		|   Nexus One BlackRose 120421  |
		|  Made by Lecahel(XDA-dla5244) |
		|    Dok-Do belongs to KOREA    |
		-------------------------------
		1    Apply stock/custom BlackRose
		2    Disable HBOOT flashing protect
		3    Uninstall BlackRose
		4    More information
		5    Exit
		Please make a decision:1

Choose 1: `Apply stock/custom BlackRose`

		Select which you want
		1    Stock BlackRose
		2    Custom BlackRose
		3    Back
		Please make a decision:2


Choose 2: `Custom BlackRose`. 

	:::text
	error: device not found
	Input BlackRose password(48 char max):

Ignore the `error: device not found` erro and insert a blank password. Just press Enter.

	Nexus One BlackRose Editor
	If you want resize partition, type [resize] and press enter key:resize
	SYSTEM + CACHE + USERDATA = 436MB
	system size(MB):280
	cache size(MB):20
	label name(20 char max):ARL

Here is the root of all problems: size partitions. 

1. Set `280` for the `/system` partition. If you [don't do so](http://forum.xda-developers.com/showthread.php?p=30852043), the installation will fail with `symlink: some symlinks failed` because MIUI 2.9 demands a lot of space.
2. Set 20 MB for cache. Otherwise your phone might not boot and enter into a loop.
3. Set whatever as the label.

	system size:280MB cache size:20MB userdata size:136MB
	label:ARL
	* Customize success
	sending 'hboot' (512 KB)... OKAY
	writing 'hboot'... OKAY
	rebooting into bootloader... OKAY
	* Stock/Custom BlackRose has been successfully applied

Done here!

## Install the ROM

1. Get the [MIUI 2.9.29 ROM](MIUI.us_passion_v4.1_2.9.29_0xD34D.zip) (or the latest one from the [MIUI website](http://roms.miui.us/))
		$ md5sum MIUI.us_passion_v4.1_2.9.29_0xD34D.zip
		6433fa6970e65e910c9ebd77cb81563b  MIUI.us_passion_v4.1_2.9.29_0xD34D.zip
1. Put the image into your sdcard root directory 
		- You can do so from recovery itself by going to `storage` and setting the storage mode on.
1. Now **wipe both caches**
1. Use the `install image from zip` and select the ROM.
1. Cross your fingers.
1. Reboot your phone after the installation has finished.


Used links and further documentation:

- [Blackrose thread](http://forum.xda-developers.com/showthread.php?t=1270589)
- [Blackrose resize video-tutorial](http://www.youtube.com/watch?v=ScIMetgk7Zw)
- [Blackrose tutorial](http://forums.miui.us/showthread.php?21396-Tutorial-Blackrose-(Custom-Hboot)-Installation-for-Miui-2-6-8)
- [Blackrose manual howto](http://www.nexusoneforum.net/forum/nexus-one-development-hacking/13358-s-off-nexus-one.html): 
- [Cyanogen wiki nexus one fastboot](http://wiki.cyanogenmod.com/wiki/Nexus_One:_Full_Update_Guide)
- [Cyanogen wiki fastboot](http://wiki.cyanogenmod.com/wiki/Fastboot)
- [Cyanogen wiki ADB](http://wiki.cyanogenmod.com/wiki/ADB)
- [Cyanogen wiki udev rules](http://wiki.cyanogenmod.com/wiki/Udev)
- [symlinks install errors](http://forum.xda-developers.com/showthread.php?p=30852043)
- [Use skip when executing Blackrose](http://forum.xda-developers.com/showthread.php?t=1842419)
