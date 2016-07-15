---
layout: post
title: Dual boot OS X and Ubuntu 16.04 with encrypted partitions
permalink: /dual-boot-osx-and-ubuntu-16.04-with-encrypted-partitions.html
published: true
categories: ubuntu linux
---

## Partition layout on MacbookPro11,5

`sda1`: Existing EFI boot Partition \\
`sda2`: Existing OSX installation \\
`sda3`: Existing Recovery partition \\
`sda4`: New /boot partition \\
`sda5`: New encrypted lvm partition for swap and `/`

## Detailed guide
I followed this guide ➡ <https://coderwall.com/p/amvmaa/dual-booting-os-x-and-ubuntu-16-04-on-macbook-pro-10-1-with-encrypted-root>

### General links
These pages were also helpful:

<https://help.ubuntu.com/community/MacBookPro>\\
<https://www.jayway.com/2015/11/22/ubuntu-full-disk-encrypted-macosx/> \\
<https://iocrunch.com/2014/02/linux-dual-boot-on-mac-with-full-disk-encryption/>

## General steps
1. Unencrypt (if not already) OSX partition to resize and make space for Ubuntu.
2. Download Ubuntu image: <http://www.ubuntu.com/download/desktop/thank-you?country=US&version=16.04&architecture=amd64> and make bootable usb disk.
3. Boot usb disk and choose 'Try Ubuntu' option.
4. Create an ext2 /boot partition (~512M should be plenty) and an ext4 partition to hold the lvm partition.
5. Create encrypted luks partition with LV's for Ubuntu installation `/` and encrypted swap (at least size of RAM, slightly bigger for hibernation - <https://help.ubuntu.com/community/SwapFaq#Example_Scenarios>).
6. Unmount any mounted partitions that will be used in the install.
7. Run the installer manually specifying the `/boot` partition in step 4 and the LV's created for `/` and swap in step 5, without rebooting or installing a bootloader, and exit installer.
8. Set up a UEFI bootloader. There are lots of ways to do this. The link below uses systemd-boot, which works fine. Ubuntu mounts the EFI partition (`sda1`) at `/boot/EFI`.

### Bootloaders

<https://wiki.archlinux.org/index.php/Arch_boot_process> \\
<https://wiki.archlinux.org/index.php/Boot_loaders>

#### systemd-boot

```
bootctl install --path=/boot/efi
efibootmgr -c -d /dev/sda1 -p 1 -l EFI/systemd/systemd-bootx64.efi -L "Linux Boot Manager"
```

#### Grub EFI
```
apt install grub-efi
grub-install --target=x86_64-efi --efi-directory=/boot/efi --bootloader-id=grub
grub-mkconfig -o /boot/grub/grub.cfg
```

#### rEFInd
sudo apt-add-repository ppa:rodsmith/refind
sudo apt update
sudo apt install refind

## Notes, fixes and tweaks

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

Fn keys by default, fn+<key> for multimedia keys
`echo 2 | sudo tee /sys/module/hid_apple/parameters/fnmode`

Swap cmd and alt keys:
`echo 1 | sudo tee /sys/module/hid_apple/parameters/swap_opt_cmd`

Clone repo at https://github.com/free5lot/hid-apple-patched and install:

```
./build.sh
./install.sh
```

Put options in `/etc/modprobe.d/hid_apple.conf`

```
options hid-apple iso_layout=1
options hid_apple fnmode=2
options hid_apple swap_opt_cmd=0
options hid_apple swap_fn_leftctrl=1
options hid_apple ejectcd_as_delete=0
```

```
sudo update-initramfs -u -k all
```

<http://superuser.com/questions/245138/linux-ubuntu-apple-aluminium-keyboard-remap-greater-less-with-tilde>

### Fix usb at boot
echo XHC1 > /proc/acpi/wakeup

### Fix Suspend issue

Kernel patch here: <https://bugzilla.kernel.org/show_bug.cgi?id=103211#c172>

#### Recompile patched kernel source
```
sudo apt-get build-dep linux-image-$(uname -r)
```

```
fakeroot debian/rules clean
DEB_BUILD_OPTIONS=parallel=2 fakeroot debian/rules binary-headers binary-generic
```

<https://wiki.archlinux.org/index.php/MacBookPro11,x#Suspend>

### Backlight issues
<http://ubuntuforums.org/showthread.php?t=2227981> \\
<https://wiki.ubuntu.com/Kernel/Dev/KernelGitGuide?action=show&redirect=KernelTeam%2FKernelGitGuide> \\
<https://bugzilla.kernel.org/show_bug.cgi?id=105051>

### LightDM dpi
<http://askubuntu.com/questions/540863/how-can-i-make-lightdm-scale-on-a-hidpi-screen>
