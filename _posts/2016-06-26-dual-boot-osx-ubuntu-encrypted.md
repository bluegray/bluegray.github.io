---
layout: post
title: Dual boot OS X and Ubuntu 16.04 with encrypted partitions
permalink: /dual-boot-osx-and-ubuntu-16.04-with-encrypted-partitions.html
published: true
categories: ubuntu linux
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
https://wiki.archlinux.org/index.php/Boot_loaders

### Install Grub EFI
`apt install grub-efi`
`grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub`
`grub-mkconfig -o /boot/grub/grub.cfg`

### rEFInd
sudo apt-add-repository ppa:rodsmith/refind
sudo apt update
sudo apt install refind

### systemd-boot
```
bootctl install --path=/boot/efi
efibootmgr -c -d /dev/sda1 -p 1 -l EFI/systemd/systemd-bootx64.efi -L "Linux Boot Manager"
```
### Detailed guide
➡ https://coderwall.com/p/amvmaa/dual-booting-os-x-and-ubuntu-16-04-on-macbook-pro-10-1-with-encrypted-root

### Fix wifi
http://askubuntu.com/questions/622988/wifi-issues-with-macbook-pro-retina-early-2015-12-2-on-ubuntu-15-04

- Uninstall any old drivers: `dpkg -r bcmwl-kernel-source`
- Download brcmfmac43602 firmware from the Linux firmware git repo: https://git.kernel.org/cgit/linux/kernel/git/firmware/linux-firmware.git/plain/brcm/brcmfmac43602-pcie.bin
- Create the folder /lib/firmware/brcm if it doesn't already exist.
- Copy the file to /lib/firmware/brcm
- Reboot and enjoy your WiFi.

#### Alternative wifi firmware (wrong drivers for my mac)
sudo apt remove --purge bcmwl-kernel-source
sudo apt install firmware-b43-installer

broadcom-wl-5.100.138.tar.bz2


### Change keyboard fn key behaviour
https://help.ubuntu.com/community/AppleKeyboard

### Fix usb at boot
echo XHC1 > /proc/acpi/wakeup

### General links
https://help.ubuntu.com/community/MacBookPro
https://www.jayway.com/2015/11/22/ubuntu-full-disk-encrypted-macosx/
https://iocrunch.com/2014/02/linux-dual-boot-on-mac-with-full-disk-encryption/

### Suspend issue?
https://wiki.archlinux.org/index.php/MacBookPro11,x#Suspend

### Backlight issues
http://ubuntuforums.org/showthread.php?t=2227981

https://wiki.ubuntu.com/Kernel/Dev/KernelGitGuide?action=show&redirect=KernelTeam%2FKernelGitGuide
https://bugzilla.kernel.org/show_bug.cgi?id=105051
