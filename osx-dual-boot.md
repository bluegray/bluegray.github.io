---
layout: page
title: Dual boot OS X and Ubuntu 16.04 with encrypted partitions
permalink: /dual-boot-osx-and-ubuntu-16.04-with-encrypted-partitions.html
published: true
---

# Outline

* Unencrypt (if not already) osx partition to resize and make space for ubuntu.
* Download ubuntu image: http://www.ubuntu.com/download/desktop/thank-you?country=US&version=16.04&architecture=amd64 and make bootable usb disk.
* Boot usb disk and choose ‘Try Ubuntu’ option 
* Create /boot partition
* Create encrypted luks partitions for encrypted swap and ubuntu installation
* Unmount any mounted partitions? Not sure if I did this
* Run the installer manually without rebooting or installing a bootloader. 
* Set up bootloader. There are lots of ways to do this. The link below uses systemd-boot, which works…

### Bootloaders
https://wiki.archlinux.org/index.php/Arch_boot_process

### Detailed guide
➡ https://coderwall.../dual-booting-os-x-and-ubuntu-16-04-on-macbook-pro-encrypted

### Fix wifi
http://askubuntu.com/questions/622988/wifi-issues-with-macbook-pro-retina-early-2015-12-2-on-ubuntu-15-04


### Fix usb at boot
echo XHC1 > /proc/acpi/wakeup

### General links
https://help.ubuntu.com/community/MacBookPro
https://www.jayway.com/2015/11/22/ubuntu-full-disk-encrypted-macosx/
https://iocrunch.com/2014/02/linux-dual-boot-on-mac-with-full-disk-encryption/

### Suspend issue?
https://wiki.archlinux.org/index.php/MacBookPro11,x#Suspend
