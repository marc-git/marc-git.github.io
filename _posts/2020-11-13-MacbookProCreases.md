---
layout: post
title: "Ironing out the creases in the configuration of the 14,3 Macbook Pro on Linux (debian)"
date: 2020-11-13
---

### This page is currently in progress

Setting up the 2017 Macbook Pro with Linux was hard in 2017 but has become a dream thanks to the work of many at the github pages of e.g. [roadrunner2](https://github.com/roadrunner2/macbook12-spi-driver) and [Dunedan](https://github.com/Dunedan/mbp-2016-linux).  Nonetheless everytime I do a reinstall I wish I had noted the small details that got a few of the creases in the configuration ironed out. That is the purpose of this page.

I always run [Debian](https://www.debian.org/) sid so this will all be related to that distro.  At the time of writing I am running with kernel 5.9.0-2 which now includes the applespi driver developed by roadrunner2.  

# Touchbar 

Not sure because it isn't compiling anymore according to the instructions on roadrunner2's page. Will have to follow up on that. 

# Wifi 

The driver is the non-free Broadcom BCM43602.  [AndyHolst](https://github.com/andyholst/BCM43602_macbookpro) has hacked the configuration file to get 2.4GHz and 5GHz channels working well enough.

# Sudo

I always need to look this up. As root:

```
usermod -aG sudo user
```

Here `-a` means append, `-G` means to group, and `user` should obviously be replaced accordingly. 

# Font Dpi
In XFCE under `Settings Manager -> Appearance -> Fonts -> Custom DPI Settings` you can increase the font to avoid squinting.
In Firefox the parameter `layout.css.devPixelsPerPx` under `about:config` can be changed to 1.5 to increase fonts by 50%.
While in Firefox I change the default search engine to [DuckDuckGo](https://duckduckgo.com/) for obvious reasons.

# Keyboard
German keyboard has letters ^ and < swapped.  Not sure how to switch it now

#Touchpad
The touchpad is active while typing which is annoying. Not sure how to disable.

# Packages

```
sudo apt install thunderbird gparted scipy redshift refind
```

# Partitioning

I install to an SSD using Guided Paritioning on the whole disk.  I usually like to keep a FAT partition unencrypted though so I utilise only part of the space available (option comes up during install).
I do

```
sudo parted
```
then
```
resizepart 3
```
take care to convert bytes to blocks by dividing by 512.

Then 
```
sudo cryptsetup resize /dev/mapper/sda3_crypt 
sudo pvresize /dev/mapper/sda3_crypt 
``` 

More to come.
