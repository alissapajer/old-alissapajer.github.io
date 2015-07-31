---
title: New Lappy
author: LuneTron
---

My new refurbished Lenovo ThinkPad X220 has arrived. This post will document all the things I do in order to get it up and running, possibly in some detail.

Step 1: It shipped without an OS, so I need to install one of those. I've decided on [NixOS](http://nixos.org/), because, purity. The hardware does not include a CD drive, so I will need to boot from a USB drive.

I've determined I have a 64-bit processor (Intel Core i5-2520M), so I've downloaded the "Graphical live CD 64-bit" version of NixOS. It's 918.9MB.

Next step is creating a bootable USB drive from an ISO image. I'm going to attempt to use [UNetbootin](http://unetbootin.sourceforge.net/) to do this. Following instructions from [this wiki](https://nixos.org/wiki/Installing_NixOS_from_a_USB_stick). 12 of the 13 files extracted quickly, but they all made it in the end.

I've now been informed by UNetbootin that: "The created USB device will not boot off a Mac. Insert it into a PC, and select the USB boot option in the BIOS boot menu." Clicking Exit.

Now I'm following instructions from [here](http://nixos.org/nixos/manual/sec-installation.html#sec-booting-from-usb). I am not running a UEFI installation, so I should simply be able to boot the OS from my USB stick without any further doings.

I inserted the USB drive into the port and started up the laptop, but it seems the BIOS isn't checking the USB prot for something bootable. So how to fix that? To get access to the BIOS, I pressed F1 a second or so after turning on my machine. Now that I'm in BIOS, I'm searching for the "boot order" option. I have found a "Boot device List F12 Option" which is already enabled. I will now restart the machine, and try pressing F12. Success. Selection the "USB HDD" option.

I got distracted for a second, and so I think it defaulted to the "Default" boot. Something went wrong, namely exactly what's reported [here](https://github.com/NixOS/nixpkgs/issues/3350). Maybe this is a UEFI issue. I changed the boot to Legacy only in the BIOS. That didn't help.

```
/Users/alissapajer$ sudo diskutil rename /Volumes/PRECOG/ NIXOS_ISO
Volume on disk3s1 renamed to NIXOS_ISO
```

The reformatted to FAT32 using mac's disk utility.

This time:
```
Non-system disk
Press any key to reboot
```

Choosing "NixOS LiveCD"

black screen...

///////////
Boot Mode: Diagnostics

Following instructions [here](http://nixos.org/nixos/manual/sec-installation.html). The `start display-manager` took a couple minutes. It was first a black screen, then a black screen with a cursor, but then desktop icons showed up!

I need internets now. Clicked on the little wifi symbol with a question mark over it in the bottom right, and connected to my wifi router. Then prompted for KWallet. I'll decide on that later.

Quick `ping www.google.com` shows me I indeed have the internets.

Now there is some partitioning to do.

```
mount /dev/disk/by-label/NISOX_ISO /mnt/
```

That was fast! `setting root password`


http://polycrystal.org/posts/2014-10-27-installing-nix.html

http://bluishcoder.co.nz/2014/05/14/installing-nixos-with-encrypted-root-on-thinkpad-w540.html

///////////////////////////////////

(1) Burn ISO CD.

(2) Press F12 at startup and choose to boot from the CD drive.

(3) log in as `root` with empty password.

https://wiki.archlinux.org/index.php/Wireless_network_configuration

iwlist wlp3s0 scan | less

iwconfig

ip link set wlp3s0 up

dmesg | grep firmware

dmesg | grep iwlwifi

/////////////////////////////////////////////////////////////////////

Starting from the beginning with the official [installation documentation](https://nixos.org/nixos/manual/sec-installation.html). We begin by booting NixOS from CD. Press F14 to enter the Boot Menu. From there, choose USB CD.

When all goes well, log in as `root` with the empty password. At this point, I am greeted with a prompt `[root@nixos:~]#`. At this point one could run `start display-manager` to start the graphical desktop KDE. I opt not to do this, and to run the installation from command line. 

In order to install NixOS, we need networking. `ip a` shows that WLAN is potentially available under `wlp3s0`. The may have other names on other systems, for example `wlan0`. 

NixOS uses wpa_suplicant for wireless networking. Check that the wpa_supplicant daemon is running with `systemctl status wpa_supplicant.service`. 

Edit `/etc/wpa_supplicant.conf` to contain:

```
ctrl_interface=/run/wpa_supplicant
update_config=1
```

and restart the supplicant service with `systemctl restart wpa_supplicant.service`. I don't know why, but this doesn't return a prompt on my machine. but checking the status after `ctlr-C`ing the command shows that it has just restarted.

///////////////////////////////////////

Starting from the beginning with the official [installation documentation](https://nixos.org/nixos/manual/sec-installation.html). We begin by booting NixOS from CD. Press F14 to enter the Boot Menu. From there, choose USB CD.

When all goes well, log in as `root` with the empty password. At this point, I am greeted with a prompt `[root@nixos:~]#`. At this point one could run `start display-manager` to start the graphical desktop KDE. I opt not to do this, and to run the installation from command line. 

/////// 13. Juni ///////////

(1) Turn on laptop with CD drive plugged in. Press F12 to access the Boot Menu upon startup. Once in the Boot Menu, select USB CD to boot from the drive. Then select the NixOS install option. There is also the option to run MemTest, which I did not run.

(2) When presented with a `nixos login:` prompt, enter `root` as the user name with no password.

(3) Now we partition the disk. Run `fdisk /dev/sda`. None of the changes you make will be saved until you write the table to disk. See the help menu `m` for how to quit without saving changes. I am installing NixOS as my main operation system, so first, I delete all existing partitions. Then create a new partion using all defaults and setting the size to `+256M`. Enable the boot flag on this partition. Then create a second partition for the remaining space. In my case, the bootable partition is `/dev/sda1` and the other partition is `/dev/sda2`. Now write the partition table to disk and exit.

(4) Next encrypt the main partition and create logical partitions within it for swap and root.

```
cryptsetup luksFormat /dev/sda2
cryptsetup open --type luks /dev/sda2 luksroot
pvcreate /dev/mapper/luksroot
vgcreate vg /dev/mapper/luksroot
lvcreate -ay --size 8G --name swap vg
lvcreate -ay -l 100%FREE --name nixos vg
```

(5) Next make filesystems on the logical volumes and mount them.

```
mkswap -L swap /dev/mapper/vg-swap
mkfs.ext4 -L nixos /dev/mapper/vg-nixos
mkfs.ext4 -L boot /dev/sda1
swapon /dev/disk/by-label/swap
mount /dev/disk/by-label/nixos /mnt
mkdir /mnt/boot
mount /dev/disk/by-label/boot /mnt/boot
```

(6) Generate the NixOS configuration file: `nixos-generate-config --root /mnt`. To edit this file, `vim` and `nano` are both available. Now we configure `/mnt/etc/nixos/configuration.nix` to suit our needs.

```
boot.loader.grub.device = "/dev/sda";
boot.initrd.luks.devices =
  [ { name = "luksroot"; device = "/dev/sda2"; }
  ];
fileSystems."/".device = pkgs.lib.mkForce "/dev/disk/by-label/nixos";
swapDevices =
  [ { device = "/dev/disk/by-label/swap"; }
  ];
```

(7) Next we need a network connection so that the installer can download what it needs. We use `wpa_supplicant` for wireless; to enable this, enable `networking.wireless.enable = true;` in the file `configuration.nix`.

Examine the wireless interface: `iw dev`. The interface name on my machine is `wlp3s0`. I have read that it is often `wlan0`, but for me it is not.

Check that the wireless interface is running: `ip link show wlp3s0`. It's running if the word "UP" is in the first set of braces after the interface name.

Determine if we are connected to a network: `iw wlp3s0 link`. This returns "Not connected.", as expected. We need to fix this.

Find out if the desired wireless network is indeed available: `iw wlp3s0 scan | less`. You can then search the output for your wireless network name. In my case, the security is RSN, which is WPA2, and so we use `wpa_supplicant` to connect to the network.

Now provide the network credentials: `wpa_passphrase <network-name> > /etc/wpa_supplicant.conf`. Then on the next line type the network password and press Enter. `cat /etc/wpa_supplicant.conf` to verify the results.

Disable NetworkManager for good measure: `systemctl stop network-manager`. Not sure if this was needed or not, but I did it.

Now for whatever reason, my `wpa_supplicant` got itself into a stuck state. It is supposed to already be running, but mine got stuck in the "activating" phase, and never actually started. This could be seen with `systemctl list-units` or with `systemctl status wpa_supplicant.service`. It wouldn't stop or restart, so I killed the PID provided in the status output. And then started it up again with `systemctl start wpa_supplicant.service`.




Failed to get D-Bus connection unknown error -1



