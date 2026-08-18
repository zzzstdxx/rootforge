
## NOTE: THIS DISTRO IS AMD64 ONLY ##
## INSTALLATION BELOW ##

## THIS PROJECT IS STILL VERY EARLY AND EXPECT TO ENCOUNTER ISSUES WITH PACKAGE COMPATABILITY WITH YOUR SETUP ##

# Rootforge
Rootforge GNU/Linux is a Linux Distribution based off Linux From Scratch targeted at expert users
It's mostly a base of Linux From Scratch with flatpak, wayland, sway, foot terminal, fuzzel app launcher and some more stuff. This distro is meant to be crafted by the user to be something he likes, by example you can add a package manager, install Xorg if you dont like the default wayland, you are free to do anything you like.

## If you need help compiling / installing stuff, you can go into the Linux From Scratch handbooks (i recommed version 12.4 because thats the version of LFS this distro is now based off) and follow the guide to compile it from there ##
# LOGO BELOW
![Rootforge Logo](rootforgelogo.png)

# Features:
- compile everything from source manually or with a custom package manager (optional)
- wayland dependent coming with very little Xorg/X11 libraries
- it's pretty easy to set up if you dont encounter any issues.
# A few known issues (read before install):
- gcc 15.2 which comes preinstalled wasn't configured yet to work with pentium III or older, 32 bit only CPUs, AMD Athlon XP or older and any other non x86_64 architectures; Your CPU also has to support AVX, SSE, SSE2, SSSE3, CX16; if not, you have to compile GCC in the Chroot enviorment following a LFS guide.
- as I said already there can be issues with vulkan, mesa or other graphical stuff. For me, everything works, I got a gtx 1060 and a i7 3770k. If you encounter any issues with graphical stuff you can recompile it for your specs.
- tarball is pretty chunky because this should be a decent base but I am working on keeping stuff light
- only UEFI support is confirmed

# Version 0.3 Handbook #
## Preparations: ##
1. You will need a USB stick
2. Get the **[Gentoo LiveGUI — `livegui-amd64-20260811T083102Z.iso`](https://www.gentoo.org/downloads/amd64/)**
3. Flash the image to a USB stick using BalenaEtcher or Ventoy
4. Boot the USB stick
5. Open a terminal, get connected to the internet and open Firefox to view this guide
6. From the terminal, run ``sudo su root`` to change the user to root
## Preparing filesystems ##
1. Run ``cfdisk`` and format your disk partitions accordingly (i recommend SWAP parition, EFI parition and root partition)
2. EFI partition should be 1G, swap partiton should be as big as you want and the root partition should take the rest of the disk space (biggest partition)
3. Create an ext4 filesystem for you root parition (example if /dev/sda3 is root parition: ``mkfs.ext4 /dev/sda3``)
4. Create a FAT32 filesystem for your boot partition (example if /dev/sda1 is boot parition: ``mkfs.fat -F32 /dev/sda1``)
5. Create a swap filesystem for your swap partition (example if /dev/sda2 is swap partition: ``mkswap /dev/sda2``)
6. Run ``mkdir -p /mnt/rootforge`` to create the mountpoint
## Mounting and Chrooting into the enviorment ##
1. First off, mount your root partition into ``/mnt/rootforge`` by doing ``mount /dev/<<rootname>> /mnt/rootforge``
2. Verify this by running lsblk; if your root partition is mounted at ``/mnt/rootforge`` you're good
3. Go into ``/mnt/rootforge`` by running ``cd /mnt/rootforge``
4. Go to ``https://sourceforge.net/projects/rootforgelinux/files/`` using a browser and download the latest .tar.xz tarball into /home/gentoo/Downloads
5. Then in the terminal, run ``cp /home/gentoo/Downloads/rootforge-* /mnt/rootforge``
6. Then extract the tarball by running ``tar xpvf rootforge-* --xattrs-include='*.*' --numeric-owner -C /mnt/rootforge``
7. Run ``ls`` and if you see the Linux file structure you're good
8. If when running ``ls`` you see a leftover ``boot/`` partition you should remove it by doing ``rm -rf /mnt/rootforge/boot/``
9. Create some directories:

``mkdir -p /mnt/rootforge/{dev,proc,sys,run}``

``mkdir -p /mnt/rootforge/{boot/efi,tmp,mnt,media}``

``chmod 1777 /mnt/rootforge/tmp``
## Chroot into the enviorment: ##
`````
mount --rbind /dev /mnt/rootforge/dev
mount --make-rslave /mnt/rootforge/dev

mount -t proc proc /mnt/rootforge/proc

mount --rbind /sys /mnt/rootforge/sys
mount --make-rslave /mnt/rootforge/sys

mount --rbind /run /mnt/rootforge/run
mount --make-rslave /mnt/rootforge/run
`````
then:
````
mount /dev/sda1 /mnt/rootforge/boot/efi
````
ALSO BEFORE CHROOTING RUN ``cp /etc/resolv.conf /mnt/rootforge/etc/`` for the network to run properly
and finally:
````
chroot /mnt/rootforge/ /bin/bash
````
then verify: ``lsblk``; if your root filesystem is now at ``/`` and not ``/mnt/rootforge`` then you're good
also don't forget to run ``source /etc/profile``
## Modifying /etc/ configs ##

1. Edit /etc/fstab, at the end should look like this:
````
# Begin /etc/fstab

# file system  mount-point    type     options             dump  fsck
#                                                                order



## IF /dev/sda3 IS ROOT
/dev/sda3      /              ext4     defaults            1     1
## IF /dev/sda2 IS SWAP
/dev/sda2      swap           swap     pri=1               0     0
proc           /proc          proc     nosuid,noexec,nodev 0     0
sysfs          /sys           sysfs    nosuid,noexec,nodev 0     0
devpts         /dev/pts       devpts   gid=5,mode=620      0     0
tmpfs          /run           tmpfs    defaults            0     0
devtmpfs       /dev           devtmpfs mode=0755,nosuid    0     0
tmpfs          /dev/shm       tmpfs    nosuid,nodev        0     0
cgroup2        /sys/fs/cgroup cgroup2  nosuid,noexec,nodev 0     0

## BOOT PARITION
#/dev/sda1      /boot/efi      vfat     defaults                   0     2
 End /etc/fstab
efivarfs /sys/firmware/efi/efivars efivarfs defaults 0 0
````
But make sure that everything matches accordingly

2. Run ``echo 'rootforge' > /etc/hostname`` to set the hostname to 'rootforge'

4. Edit /etc/hosts/

Should look like:
````
# Begin /etc/hosts

127.0.0.1 localhost
127.0.1.1 rootforge
::1       localhost


# End /etc/hosts
~                    
````

Make sure that your hostname matches the file

4. Edit /etc/doas.conf

````
permit nopass user
````
Make sure to replace ``user`` with you actual desired username that we will get into soon
Also ``nopass`` means the user doesn't have to put in a password to use doas
If you want the user to use a password for doas change ``nopass`` to ``persist``

5. Edit /etc/locale.conf

It has instructions inside the file, set it to your preffered locale, if not en_US.UTF-8 will work fine
## Compiling the Kernel ##

This is where installs go to graces if you don't know how to correctly configure the Kernel im sorry but that's on you
I recommend for everyone to check the LFS 12.4 page about this Kernel, it lists needed configurations for LFS which if you don't remember this Distro is based off LFS 12.4 so you'll need those configurations too.
**[LFS 12.4 Linux-6.16.1](https://www.linuxfromscratch.org/lfs/view/12.4/chapter10/kernel.html)**

1. ``cd`` into /usr/src: ``cd /usr/src``
2. Extract the Linux source code: ``tar -xvf <name>``
3. ``cd`` into the Linux directory
4. Run ``make mrproper`` for a fresh directory
5. Run ``make defconfig`` for the default config
6. Run ``make menuconfig`` and compile the Kernel to your hardware
7. Run ``make -j8 && make modules_install`` to compile the Kernel (might take a while)
8. If that finishes peacefully then run ``cp /arch/x86_64/boot/bzImage /boot/vmlinuz-6.6.1``, ``cp System.map /boot/`` and finally ``cp .config /boot/config-6.6.1``
## GRUB ##
1. To install grub for most users on UEFI run ``grub-install /dev/<<root>>``
2. Then run ``grub-mkconfig /boot/grub/grub.cfg``
## Reboot ##
1. Now you should be able to reboot.
2. First off, exit the Chroot enviorment by running ``exit``
3. Then, you should unmount your disk paritions (optional) by running ``umount -R /mnt/rootforge``
4. Reboot and you should be able to use your system.

# Post install will be coming soon for now you'll be fine getting information off Linux From Scratch 12.4 Handbook #
