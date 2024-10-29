---
title: Installing Arch for dummies (me)
description: Arch Install walkthrough for future reference. 
date: 2024-10-29
authors: 
  - externref
categories:
  - linux
  - arch
comments: true
---

# Installing Arch for dummies (me)

[Arch Linux](https://archlinux.org/) is a lightweight and flexible Linux distribution that provides full customisation options with minimal pre-install configuration options and less bloatware. The installation and setup process, however, is a bit complicated and different than most daily dirving Linux Distros like Ubuntu, Fedora, etc...

Even though there are custom ISOs with GUI installers available, its safer to use the officially released ISOs version. This blog is a walkthrough for steps involved in installation of the OS with the `i3-wm` desktop environment. All these instructions can be found on [The Installation guide](https://wiki.archlinux.org/title/Installation_guide) with some difference in commands used.

!!! note
    The screenshots are from a VirtualBox setup for a clearer demonstration of the process. The installation process is same.

My dotfiles: [@externref/dotfiles](https://github.com/externref/dotfiles)

## Installating the OS

### Creating an Installation Medium
To install arch you need to load it into an installation medium like a USB Drive, Optical Disc or any memory device.

![rufus](/assets/arch_install/rufus_home.png){ width="300" align="right" }

* First, download the ISO file from one of the sources listed on the [download page](https://archlinux.org/download/). You need the file with `.iso` extension from the respective sources.
* Setup your utility for creating the bootable drive, we are going to use [Rufus](https://rufus.ie/en/) which is a lightweight tool for flashing USBs. (You might want to use the [Balena Etcher](https://www.balena.io/etcher) utility on a linux distribution.)
* Download and launch the rufus executable and select the USB device and the ISO image that you downloaded. Click on Start and proceed further with the recommended options.

### Booting and Pre Install configs.

Restart the System to open the BIOS menu and select the device that was flashed with the Arch ISO.

!!! warning
    Make sure to disable `Secure Boot` option from your system settings during the installation process, you can re-enable it after installing.

On booting, you should enter into a Grub screen with Arch Linux Installation medium and other options.

![grub1](/assets/arch_install/grub1.png)

Upon booting you should see the arch installation terminal with some instructions.

#### Connect to internet
Before anything, you should be connected to the internet. 
Run the `iwctl` utility to open an interface to connected to the desired network.

* Use the `device list` to list all the available devices, if you're using a WIFI network it should be a wlan network.
* Scan the station using `station [wlan_name] scan`.
* Connect to the desired network using `station [wlan_name] connect [device_name]`.
#### Verify internet connection

Use the `ping` command to check internet connection. It should produce an output like this 

![ping](/assets/arch_install/ping.png)

### Paritioning and Formatting Disks
You need 2 disk partitions in order to install Arch Linux.

disk | recommended memory | mount point | fs 
-----|--------------------|-------------|-----
boot | 1GiB               | `/mnt/boot` | FAT32
root | ~30-40GiB          | `/mnt`      | EXT4

#### Disk partitions

Use the `lsblk` command to check the current memory condition.

![lsblk](/assets/arch_install/lsblk_pre.png)

Remember the address of the disk where the system is to be installed.

Use the `cfdisk` command to create partition. You need an unallocated space of around 40 GiB minimum to install daily driving arch.

Use `cfdisk /dev/<disk_name>` to open the cfdisk interface.

![cfdisk](/assets/arch_install/cfdisk.png)

You can see the empty patition in the screenshot, we'll be using this to create our arch patitions.

* Create a 1GiB partition and give it type EFI System.
* Allocate the rest to the linux filesystem partition.

??? Screenshots
    ![](/assets/arch_install/cfdisk.png)
    ![](/assets/arch_install/efi_sys.png)
    ![](/assets/arch_install/write.png)
    ![](/assets/arch_install/lsblk_post.png)

#### Creating filesystems

Now, format the partitions into the required filesystem types.
```sh
mkfs.fat -F 32 /dev/<boot_partition> 
mkfs.ext4 /dev/<root_patition>
```

This command formats the disks into the required fs types.

![](/assets/arch_install/mkfs.png)

#### Mouting the filesystems

Now, we mount the filsystems on the disk.

```sh
mount /dev/<root_parition> /mnt
mount --mkdir /dev/<boot_partition> /mnt/boot
```

![](/assets/arch_install/mount.png)

This was all the manual configuration, in further steps, we'll be using the [archinstall](https://wiki.archlinux.org/title/Archinstall) script for easier installation.

### Arch Install
Run the `archinstall` command in the terminal. It will open an interface stating various install options with some pre-set ones.

![](/assets/arch_install/archinstall.png)

* Go to the `Disk Configuration` option, and use the pre configured mount option from create partitions option. Enter `/mnt` as the value and press enter. Confirm and exit.
* Select `Grub` as the bootloader.
* In the User settings create a new user with desired configurations.
* Profile configs [below]
* Select `Pipewire` or `Pulseaudio` for the audio server.
* Use `NetworkManager` for the network configs.
* Select your timezone by searching it throught `/`.

##### Profile
We'll be using [i3-wm](https://i3wm.org/) for this installation, you can choose the desktop environment you want and continue with it.

* Go to `Profile` and select `Desktop`.
* Select `i3-wm` (using Tab) in the desktop environment options.
* Select `ly` as the login screen.

Save and go back to main screen.


This is what your configs should look like after setting up everything.

![](/assets/arch_install/final.png)

Select `Install` and press enter and watch the magic happen!.

You should be greeted with this screen once you're done installing arch.

![](/assets/arch_install/chroot.png)

Enter in prompt if you have anything else to configure. 

**Your Arch Linux installation is not completed and can be booted into.**