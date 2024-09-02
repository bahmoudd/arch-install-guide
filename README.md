> Tested working as of release 2024.09.01

# The ultimate Arch linux install guide (UEFI & MBR)

## Table of Contents
 - [**Glossary**](#glossary)
 - [**Introduction**](#introduction)
 - [**Re-sizing your C:\ drive to allow for dualbooting)**](#resize-c-drive)
 - [**Booting into the ArchISO**](#boot-to-archiso)
   - [**Creating the installation medium**](#creating-boot-medium)
   - [**Entering your computer's boot menu**](#entering-boot-menu)
 - [**Connect to the Internet**](#connect-to-internet)
 - [**Disk Partitioning**](#partition-disk)
 - [**Base System Installation**](#base-system-installation)
   - [Update Mirrors](#update-mirrors-using-reflector)
   - [Base System](#install-base-system)
   - [Generate fstab](#genfstab)
 - [**Chroot**](#chroot)
   - [Date & Time](#set-time--date)
   - [Locale and Language](#set-locale-and-language)
   - [Hostname & Hosts](#set-hostname)
   - [Enabling Network Manager](#enable-networkmanager)
   - [ROOT Password](#set-root-password)
   - [Installing the Bootloader](#installing-bootloader)
     - [Adding hooks for encrypted drives](#initcpio-hooks-for-luks)
     - [Unify Kernel Images (optional)](#uki)
     - [Installing GRUB](#installing-grub)
     - [Installing SystemD-Boot](#installing-systemd-boot)
   - [Create users](#create-users)
   - [Allow sudo commands](#allow-sudo-commands)
 - [**User Login**](#reconnect-to-the-internet)
   - [Connect to the internet (again)](#reconnect-to-the-internet)
   - [Display Server & GPU Drivers](#xorg--gpu-drivers)
   - [Multilib Repository (32-bit support on 64-bit systems)](#multilib)
   - [Display Manager](#install--enable-dm)
   - [Installing a Desktop Environment](#installing-de)
   - [Audio Utilities & Bluetooth](#audio-utilities--bluetooth)
   - [Adding the Windows Boot Manager as a boot option in GRUB](#add-windows-bootmgr)
 - [**The Conclusion**](#the-conclusion)
 - [**Extras (optional)**](#extras)
   - [Misc Applications](#misc-applications)
   - [Yay](#install-yay)
 - [**Maintenance, Performance Tuning & Monitoring**](maintenance-performance-tuning--monitoring)
   - [Paccache](#paccache)
   - [Cockpit](#install-cockpit)
 - [**Changelog**](#changelog)

---

# Glossary <a name="glossary"></a>

Name            | Description
----------------| -------------------------------------------------------------------------------------------------------------------------------------------------------------------
OS              | Operating System, a kind of program that helps a computer to interact with other machines and/or with people.
Unix            | Unix is a computer OS, first developed at Bell Labs. It became very influential within academic circles, leading to large-scale adoption by start-up companies leading Unix to fragment into much smaller, similar but mostly mutually incompatible OSes. Among these are FreeBSD, OpenBSD and MacOS.
Unix-like       | Any Operating System not derived off of any Unix system, but behaves like one.
Kernel          | The middleman between the OS and the hardware.
Linux           | A Unix-like kernel created by Linux Torvalds in 1991.
Distro          | A distribution of Linux - meaning - an OS based off of the Linux kernel, these include Ubuntu, Debian, Red Hat, Fedora and Arch Linux amongst many others.
Bootloader      | A piece of software designed to load the necessary files that start your computer.
Package Manager | A package manager is a program designed to automate the process of downloading, installing, updating, listing, removing and searching for software (bundled together as an app or "package")
Greeter         | A greeter is a graphical login interface, and is often called the login screen. It essentially "greets" the user(s) to the system.
Shell           | A shell is a text environment where you issue commands and information to your computer. 
Comment         | Some text that Arch Linux ignores automatically. Comments begin with the hashtag. To comment something out, put a hastaf before it, and to uncomment something, remove the hashtag preceeding it
-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
# Introduction

## What is Arch Linux?
Arch Linux is an independently-developed rolling-release distribution of Linux. This means that there are no major updates that are delivered to Arch, unlike Windows. Instead, Arch delivers updates in small, frequent amounts, meaning that you have access to the latest drivers, firmware and software as soon as it's available. Since Arch is based off the Linux kernel, it's distributed under the GNU GPLv2 license, meaning that anyone can view the kernel, and what makes up Arch, as well as being able to modify it any point, so long as the software remains open-source.

Arch Linux is fantastic for its Arch User Repository (AUR) and simplicity of design. It is well known for its straightforwardness and is relatively easy for the end user to understand directly, rather than having to dig through multiple layers of graphical interfaces. The package manager - pacman - has no GUI, meaning that everything has to be installed through the command line. This may seem a bit scary at first, but in this guide, I will go through how to install Arch Linux, and how to use its many features.

## A few notes before you continue with the guide

- `[...]` means that there are lines present between whatever is shown
- Anything else within square brackets, e.g. ```[text]``` means you should substitute with what's between those square brackets. For example, ```[Root Partition UUID]``` means you have to put the UUID of your root partition in place of the square brackets.
- Any text after hashtags or equals signs explain the command you are running and are not to be included when you type in the command.

---

# Are you dual-booting this? <a name="resize-c-drive"></a>

If you plan on dual-booting Windows and Arch Linux, hit `Win+R` on your keyboard and type into the Run prompt:
```
diskmgmt.exe
```

This will bring up disk management. From here, you can resize your `C:\` partition to dual-boot it with Arch Linux.\
Find your `C:\` partition and right-click on it.\
Click on `Shrink Volume` and right beside `Enter the amount of space to shrink in MB`, enter the amount of drive space you'd like to allocate to Arch.\
I recommend shrinking your `C:\` partition to half of its original space.

## Creating the Arch installation medium <a name="creating-boot-medium"></a>

Firstly, get the ISO file from [the official Arch Linux website](https://archlinux.org/download/) or [the website for the community-maintained 32-bit Arch Linux OS](https://www.archlinux32.org/download/) either through torrenting it, or using one of the mirrorlinks.\
You'll need to use software such as [Ventoy](https://www.ventoy.net/en/download.html), [Rufus](https://rufus.ie/en/) or [BalenaEtcher](https://etcher.balena.io/#download-etcher). The instructions on how to use such software can be found within their websites.

# Booting into the ArchISO <a name="boot-to-archiso"></a>

## Entering the boot menu <a name="entering-boot-menu"></a>

Once the ISO has been written to a storage medium (that could be a USB thumb drive, SD card .etc), you'll need to go into your computers boot options through the firmware interface. To do so, follow the instructions below, based on your current operating system (not the operating system you want to install, which is, obviously, Arch Linux).

<details>
 <summary><h3>Windows</h3></summary>

 
 On Windows systems, open up the command prompt by hitting the Win and searching ```cmd```.\
 Make sure you run it as an administrator.\
 Once you've done that, enter the following command:
 ```
 shutdown /r /fw /t 0
 ```

 And enter your computer's boot menu. Once that's done, select the storage medium that contains your ArchISO.\
 Then, wait for it to boot before selecting the option containing ```Arch Linux install medium``` or ```Arch Linux install medium (with speech)``` if you are visually impaired, and have someone reading this guide to you out loud.
</details>

<details>
 <summary><h3>MacOS</h3></summary>

 
 On a MacOS system, there is no way through the command line to enter the boot menu.\
 You can only do so by powering off the system entirely and holding the ```Alt``` or ```Option``` key when powering it on.\
 Select the storage medium that contains your ArchISO.\
 Then, wait for it to boot before selecting the option containing ```Arch Linux install medium``` or ```Arch Linux install medium (with speech)``` if you are visually impaired, and have someone reading this guide to you out loud.
</details>

<details>
 <summary><h3>Linux</h3></summary>

 Assuming you have SystemD installed on your system, run the below command:
 ```
 sudo systemctl reboot --firmware-setup
 ```

 And enter your computer's boot menu. Once that's done, select the storage medium that contains your ArchISO.\
 Then, wait for it to boot before selecting the option containing ```Arch Linux install medium``` or ```Arch Linux install medium (with speech)``` if you are visually impaired, and have someone reading this guide to you out loud.
</details>

---

## Pre-installation configuration

Now that you've booted into the ArchISO, you need to run some commands before installing Arch to make it possible to install.

### Load Keymaps

A keymap is how your keyboard is laid out. It defines the keys you use and where they are placed on the keyboard. To find a list of all the keymaps available for your language/region, run the below command:

```
localectl list-keymaps
```

To search for a keymap, use the following command, replacing `[search_term]` with the code for your language, country, or layout:
```
localectl list-keymaps | grep -i [search_term]
```

Once you've found a keymap that matches your physical keyboard's layout, run the below command.

```
loadkeys [keymap]
```

### Connect to the internet <a name="connect-to-internet"></a>

The ArchISO *requires* an internet connection in order to be set up properly.\
If your computer is connected via Ethernet, you may skip this section by going [here](#test-connectivity).\
If it isn't however, you must connect to the internet by reading the instructions below.

Check if your access point is either a Wi-Fi router or mobile broadband modem, of which the former is more likely.

<details>
 <summary><h3>WiFi Router (iwctl)</h3></summary>

 If your access point is a Wi-Fi router, run the below command:
 ```
 iwctl
 ```
 This will bring up an interactive prompt session and change your prompt from:
 ```
 root@archiso ~ #
 ```
 to:
 ```
 [iwd]#
 ```

 If you don't know what your wireless device's name is, you can list them by running:
 ```
 device list
 ```
 To turn your wireless device and its corresponding adapter on, run the below commands:
 ```
 device [device-name] set-property Powered on
 adapter [adapter-name] set-property Powered on
 ```
 (replacing ```[device-name]``` with your device's name and ```[adapter-name]``` with its corresponding adapter's name respectively) 

 Then, to scan for Wi-Fi networks, run:
 ```
 station [device-name] scan
 ```
 To list them, run:
 ```
 station [device-name] get-networks
 ```
 Find your router's SSID amongst the list shown, and connect to it by running:
 ```
 station [device-name] connect [your router's SSID]
 ```
</details>

<details>
 <summary><h3>Mobile Broadband Modem (mmcli)</h3></summary>

 Start and enable ```ModemManager.service```, this is the service that allows you to connect to your Mobile Broadband Modem.\
 You can do so by running:
 ```
 systemctl start ModemManager.service
 systemctl enable ModemManager.service
 ```
 You can find a list of modems nearby you by running:
 ```
 mmcli -L
 ```
 Look for ```/org/freedesktop/ModemManager1/Modem/[modem index]```
 (where your modem index is a unique number representing your modem)

 Connect to your modem by running:
 ```
 mmcli -m [modem index] --simple-connect="apn=[your modem's APN]"
 ```

 Your APN is your modem's Access Point Name and will have been given to you by your ISP.
 If your modem requires a username and password, you can specify them like so:
 ```
 mmcli -m [modem index] --simple-connect="apn=[your modem's APN],user=[username],password=[password]"
 ```
 
</details>

#### Testing your internet connection <a name="test-connectivity"></a>
```ping``` is a command that sends data to a URL and checks that the data has been sent properly. This is normally done to test the URL or to test your internet connection, of which you are doing the latter.
```
ping -c 4 archlinux.org
```
> The ```-c``` switch represents the amount of sample data you are sending to that URL, in this case, 4.


### Check the system clock is correct

Your computer keeps track of time by utilising an internal hardware clock.\
To check that it's in sync with the real world time, run the below command:

```
timedatectl
```

If ```NTP service``` does not show ```active```, run the below command:
```
timedatectl set-ntp true
```

As of now, you don't have to worry about the timezone, just make sure that the UTC time it returns matches real-world UTC time

---

## Creating necessary partitions <a name="partition-disk"></a>

:warning: - Make sure that all of your data on the disk you want to install Arch Linux onto has been backed up. If you've already done that or you don't care about the data present on it, proceed with this guide. If not, exit out the ArchISO and back up your data.

Run the below command:
```
[ -d /sys/firmware/efi ] && echo UEFI || echo BIOS
```

And take note of whether it returns ```UEFI``` or ```BIOS```

### Disk Partitioning

<details>
 <summary><h3>UEFI</h3></summary>

> Note: If you're dual-booting, skip down to "Creating Partitions"

Run the below commands to initialise the disk:
```
gdisk /dev/sdx
```
> (where sdx refers to the letter of your drive)

The above will open up an interactive prompt session and change the prompt from:
```
root@archiso ~ #
```
to:
```
Command (? for help):
```

Run:
```
x
```

which will change your prompt to:
```
Expert command (? for help):
```

And enter:
```
z
```
To "zap" the disk (basically, to initialise the disk).\
It will ask you if you want to confirm, enter ```y``` and it will then if ask you want to "blank out MBR?", which means it will wipe all of the boot code and other critical data necessary for an OS to boot. Since you're no longer booting from your old OS, go ahead and hit ```y```.

#### Creating Partitions

Run gdisk again and enter the below characters, as gdisk only uses one-character commands:

 ```
n = New Partition
[simply press enter] = 1st Partition
[simply press enter] = As First Sector
+1G = As Last sector (BOOT Partition Size)
ef00 = EFI Partition Type

n = New Partition
[simply press enter] = 2nd Partition
[simply press enter] = As First Sector
+16G = As Last sector (SWAP size, or double your RAM, whichever is smaller)
8200 = Linux Swap
```

If you want to create a home partition (separates the data that is your system from your basic user apps and configurations), enter the below:

```
n = New Partition
[simply press enter] = 3rd Partition
[simply press enter] = As First Sector
+40G = As Last sector [ROOT Partition Size (you may use 20GiB if you have a small hard drive)]
[simply press enter] = Linux filesystem

n = New Parition
[simply press enter] = 4th Partition
[simply press enter] = As first sector
[simply press enter] = As last sector [HOME parition size (takes up remaining hard drive space)]
[simply press enter] = Linux filesystem

w = Write partitions and quit
```

If you don't want to do that, however, enter the below:
```
n = New Parition
[simply press enter] = 3rd Partition
[simply press enter] = As first sector
[simply press enter] = As last sector [ROOT parition size (takes up remaining hard drive space)]
[simply press enter] = Linux filesystem

w = Write partitions and quit
```

From here on out, whatever partition number the guide tells you to make changes to, add 3 to that number, so you don't accidentally make changes to your Windows OS.\
(The below is not a command, do not enter this into the Arch ISO)
```
BOOT Partition: /dev/sdx1 -> /dev/sdx4
SWAP Partition: /dev/sdx2 -> /dev/sdx5
ROOT Partition: /dev/sdx3 -> /dev/sdx6
HOME Partition: /dev/sdx4 -> /dev/sdx7
```

<details>
 <summary><h4>Encrypt your drive (optional)</h4></summary>
LUKS encryption encrypts your root partition (and home partition too, if present) so that attackers who have physical access to your laptop cannot access the contents of your drive.

To set that up, enter the following commands:

```
cryptsetup -v luksFormat /dev/sdx3
```

And if you have set up a home partition, encrypt that too.

```
cryptsetup -v luksFormat /dev/sdx4
```

Make sure to unlock them so that they can be formatted later.

```
cryptsetup open /dev/sdx3 root
```

And if you have a separate home partition, run the below command:
```
cryptsetup open /dev/sdx4 home
```
</details>

### Format non-swap partitions (so that data can be written/read from it) <a name="format--mount-partitions"></a>
```
mkfs.fat -F32 /dev/sdx1 # Done as it needs to be in a universally-recognised file system, so your computer can boot from it.
mkfs.btrfs /dev/sdx3 # Add -f if your system tells you another filesystem like ext4 is already present
mkfs.btrfs /dev/sdx4
```

Format and turn on swap memory
```
mkswap /dev/sdx2
swapon /dev/sdx2
```

### Mount Remaining Partitions (so they can be accessed)

If you have set up LUKS encryption, run the below:
```
mount /dev/mapper/root /mnt
mount -m /dev/mapper/home /mnt/home
mount -m /dev/sdx4 /mnt/home
```

Otherwise, run the below:

```
mount /dev/sdx3 /mnt 
mount -m /dev/sdx1 /mnt/boot
mount -m /dev/sdx4 /mnt/home
```

</details>

<details>
 <summary><h3>MBR</h3></summary>

To initialise the disk you want to install Arch on ("sdx" from now on), run the below command:
```
sfdisk --delete /dev/sdx
```

Then, enter the below (do not type anything where ```=``` precedes it, as those are just explanations for what each command does):
```
n = New Partition
1 = Partition number, MBR only supports 4 partitions
[simply press enter] = First sector size
+16G = (or double the size of your RAM, whichever is smaller, this is your SWAP size)

t = Change partition type ID
19 = Linux SWAP
```

If you want to create a home partition (separates the data that is your system from your basic user apps and configurations), enter the below:

```
n = New Partition
2 = Partition number, MBR only supports 4 partitions
[simply press enter] = First sector size
+20G = (this is your ROOT size)

n = New Partition
3 = Partition number, MBR only supports 4 partitions
[simply press enter] = First sector size
[simply press enter] = (this is your HOME size, takes up the rest of the disk)

x = Enter expert mode
A = Make a partition bootable
2 = Make your ROOT partition bootable

w = Write partitions and quit
```

Else, just enter the below:

```
n = New Partition
2 = Partition number, MBR only supports 4 partitions
[simply press enter] = First sector size
[simply press enter] = (this is your ROOT size, takes up the rest of the disk)

x = Enter expert mode
A = Make a partition bootable
2 = Make your ROOT partition bootable

w = Write partitions and quit
```

### Format non-swap partitions
```
mkfs.ext4 /dev/sdx2
mkfs.ext4 /dev/sdx3 # If you made a home partition
```

Format and turn on swap memory
```
mkswap /dev/sdx1
swapon /dev/sdx1
```

### Mount Remaining Partitions
```
mount /dev/sdx2 /mnt 
mount -m /dev/sdx3 /mnt/home
```

</details>

---

# Base System Installation <a name="base-system-installation"></a>

## Update Mirrors using Reflector (optional but recommended for faster download speeds, slow download speeds can time out) <a name="update-mirrors-using-reflector"></a>
```
reflector -c County1 -c Country2 -a 12 -p https --sort rate --save /etc/pacman.d/mirrorlist
```

A mirror is basically a repository of files located within a specific region so that people near that region can download files with minimal ping (as ping is how long it takes for the data you requested for reaches you).\
A mirrorlist is a list of these mirrors ranked based on their speed relative to your computer.\
The ```-c``` flag allows you to specify which country you live in, this is to reduce ping.\
The ```-a``` flag excludes mirrors that haven't been updated within the hour range specifies (in this case, 12 hours)\
The ```-p``` flag specifies the protocol you want to download packages through. HTTPS is chosen here as it is the fastest and the safest.\
```--sort``` sorts the mirrors by how fast they are, relative to your computer.\
```--save``` specifies to reflector to save them in ```/etc/pacman.d/mirrorlist```, which is where pacman refers to when you want to download a package.

Example:
```
reflector -c 'United Kingdom' -a 12 -p https --sort rate --save /etc/pacman.d/mirrorlist
```

### Install base system <a name="install-base-system"></a>
```
pacstrap -K /mnt base base-devel linux linux-firmware linux-headers nano intel-ucode mtools dosfstools networkmanager btrfs-progs
```

`base` is all the software necessary to make Arch Linux work.\
`base-devel` is software for development purposes only, such as GCC or G++, this is necessary if you want to build any software from the source code (which many packages require).\
`linux`, `linux-headers` and `linux-firmware` are the kernel, which, together, act as the middleman between the software on your computer and the hardware on your computer.\
`mtools` allows the user to access MS-DOS disks.\
`dosfstools` allows the user to format MS-DOS disks.\
`networkmanager` allows the user to connect to the internet.\
`btrfs-progs` allows userspace utilities for your disk to run.

If you're connected to the internet through a Mobile Broadband Modem, append the below packages to your command:
```
modemmanager usb_modeswitch
```

- Replace `nano` with editor of your choice (i.e `vim` or `vi`). Nano is the most beginner friendly
- Replace `intel-ucode` with `amd-ucode` if you are using an AMD Processor.
- If you want to include a derivative of the Linux kernel (e.g. linux-zen, linux-hardened, linux-lts), include that ALONGSIDE the original kernel and headers so if something breaks, you always have that extra option to use the regular Linux kernel. This can be done by appending your chosen Linux derivative (e.g. `linux-zen`) and its headers (e.g. `linux-zen-headers`)

### Generate fstab <a name="genfstab"></a>

The fstab file lists all available disk partitions and other types of file systems and data sources that may not necessarily be disk-based, and indicates how they are to be initialized or otherwise integrated into the larger file system structure. It is *essential* for your system to function.

```
genfstab -U /mnt >> /mnt/etc/fstab
```

> Note: A ```>``` will overwrite a file and a ```>>``` will append to a file. Ensure you don't confuse these with each other, and make sure the commands you type are as how this guide has written it before you hit enter.


---

## Chroot <a name="chroot"></a>

Chroot essentially takes you inside of the Arch Linux system, so you can perform necessary configuration to it for it to function. This can be done by running the below command:

```
arch-chroot /mnt
```

### Set Time & Date <a name="set-time--date"></a>

The below symbolically links ```/etc/localtime``` to ```/usr/share/zoneinfo/Region/City``` so that your computer knows it is measuring the date and time in the correct timezone.

```
ln -sf /usr/share/zoneinfo/Region/City /etc/localtime 
hwclock --systohc # Sync hardware clock with system time
```

Replace `Region` & `City` according to your Time zone. To see what timezones are available, use the following commands:
```
ls /usr/share/zoneinfo/
```
and
```
ls /usr/share/zoneinfo/[Region]
```
An example of this would be:
```
/usr/share/zoneinfo/Europe/London
```

## Set locale and language <a name="set-locale-and-language"></a>

We will use `en_GB.UTF-8` here but, if you want to set your language, replace `en_GB.UTF-8` with yours in all below instances.

#### Edit locale.gen

A locale customises your system to be tailored more towards a specific region.

```
nano /etc/locale.gen
```
Uncomment the below line (or any line, depending on your region and what language your keyboard is in) by removing the hashtag preceeding the line
```
#en_GB.UTF-8 UTF-8
```
save & exit, by hitting Ctrl+W, Enter, then Ctrl+X.

### Generate The Locale
```
locale-gen
```

### Add LANG to locale.conf
```
echo LANG=en_GB.UTF-8 > /etc/locale.conf
```

### Add Keymaps to vconsole
For keyboard users with non US Eng only. Replace `uk` with yours.
```
echo "KEYMAP=uk" > /etc/vconsole.conf
```

## Set Hostname <a name="set-hostname"></a>

A hostname is what your computer identifies itself as when talking to other computers. Set a hostname as shown below:

```
echo arch > /etc/hostname
```
Replace `arch` with hostname of your choice.

### Configure hosts file
```
nano /etc/hosts
```
#### Add these lines to it
```
127.0.0.1    localhost
::1          localhost
127.0.1.1    arch.localdomain arch
```
Replace `arch` with the hostname of your choice. Make sure it matches the hostname in ```/etc/hostname```\
Save & exit.

### Enable NetworkManager <a name="enable-networkmanager"></a>
"Enabling" a software puts in a state when you can use it - essentially turning it on.
```
systemctl enable NetworkManager
```

### Set ROOT Password <a name="set-root-password"></a>

The ROOT account is an account on your computer that can do anything on your computer. Set a password for it as not doing so leaves your system vulnerable to cybercriminals.

```
passwd
```

## Installling the bootloader <a name="installing-bootloader"></a>

The bootloader is what manages the boot process, and is the PID 0 of your Arch system.\
If you're on an MBR systems, install GRUB and if you're on an EFI system, install SystemD-Boot.\
You need to do some extra steps before installing the bootloader if you've encrypted your ROOT/HOME parition(s).

<a name="initcpio-hooks-for-luks"></a>
<details>
 <summary><h3>Enabling password prompt on boot to unencrypt your drive</h3></summary>

Edit the mkinitcpio.conf file:
```
nano /etc/mkinitcpio.conf
```

Find and comment out any line beginning with `hooks` and add the following line to your `mkinitcpio.conf` file:
```
HOOKS=(base systemd autodetect modconf kms keyboard sd-vconsole sd-encrypt consolefont block filesystems fsck)
```

Save and quit.

Regenerate the cpio as shown below:
```
mkinitcpio -P
```
</details>

<a name="uki"></a>
<details>
 <summary><h3>Unify your kernel images (optional)</h3></summary>

Make mkinitcpio quiet so your screen isn't filled with a bunch of useless garbage:
```
echo "quiet rw" > /etc/kernel/cmdline
```

Create the `/efi/EFI/Linux` directory, which is where your kernel images will be stored:
```
mkdir -p /efi/EFI/Linux
```

Edit the `/etc/mkinitcpio.d/linux.preset` file as shown below:
```
nano /etc/mkinitcpio.d/linux.preset
```

Uncomment the below lines:
```
ALL_config="/etc/mkinitcpio.conf"
[...]
default_uki="/efi/EFI/Linux/arch-linux.efi"
default_options="--splash /usr/share/systemd/bootctl/splash-arch.bmp"
[...]
fallback_uki="/efi/EFI/Linux/arch-linux-fallback.efi"
```

Save and quit.


And comment out the below lines:
```
default_image="/boot/initramfs-linux.img"
[...]
fallback_image="/boot/initramfs-linux-fallback.img"
```

Regenerate the cpio as shown below:
```
mkinitcpio -P
```

</details>

You'll still need to install a bootloader if you've set up LUKS and/or unified your kernel images. Go to one of the collapsable bootloader sections below.\
If you set up LUKS, go to "Installing and configuring SystemD-Boot (UEFI)" below.

<a name="installing-grub"></a>
<details>
 <summary><h3>Installing GRUB (MBR or if you're dual-booting)</h3></summary>
"Targets" are CPU architechtures. These are important for grub to know so it can handle the boot proess correctly.\
Find your CPU architechture from [this site](https://renenyffenegger.ch/notes/Linux/shell/commands/grub-install#grub-install-target) and specify that as the target.

```
pacman -S grub
grub-install /dev/sdx # The default is i386-pc, otherwise known as 32-bit x86
grub-mkconfig -o /boot/grub/grub.cfg
```

You can specify a CPU architechture as shown below:
```
grub-install /dev/sdx --target=[Your CPU's architecture]
```

An example of this is (assuming you're using a 64-bit x86-64 CPU, like most modern CPUS do)
```
grub-install /dev/sdx --target=x86_64-efi --efi-directory=/boot --bootloader-id=GRUB
```

GRUB needs to know your EFI directory so it can boot your system properly, which is what `--efi-directory` specifies.\
`--bootloader-id` makes GRUB known to your UEFI firmware.

</details>

<a name="installing-systemd-boot"></a>
<details>
 <summary><h3>Installing and configuring SystemD-Boot (UEFI)</h3></summary>

<details>
 <summary><h4>If you've set up LUKS encryption on your hard drive</h4></summary>

Because you've encrypted your ROOT (and HOME partition, if present), you need to perform some extra steps.

Open and edit your ```/etc/mkinitcpio.conf``` file:
```
sudo nano /etc/mkinitcpio.conf
```

Find the below line:
```
HOOKS=(base udev autodetect modconf block filesystems keyboard fsck)
```

And change it to:
```
HOOKS=(base udev autodetect keyboard keymap modconf block encrypt filesystems keyboard fsck)
```

Save and exit, then recreate the initramfs image:
```
mkinitcpio -P
```
 
</details>

Install SystemD-Boot:
```
bootctl install
```

#### Creating and amending config files 

> Note: You may skip this entire section if you've unified your kernel images

Open and edit /boot/loader/loader.conf
```
nano /boot/loader/loader.conf
```
Comment out any line beginning with ```default``` by putting a hashtag at the beginning of the line.
And add this line to the bottom of the file
```
timeout 3
console-mode keep
default arch.conf
editor no
```

```timeout 3``` stalls your system by 3 seconds before it boots so you have time to select an option. This is optional, and if you want an instant boot, you can leave it commented.\
```console-mode keep``` keeps your console-based SystemD-Boot menu to how it was last time.\
```default arch.conf``` is what SystemD-Boot automatically boots to when you don't select an option.\
```editor no``` prevents any edits to be made to the boot options within the menu itself.

Once that's done, type:
```
nano /boot/loader/entries/arch.conf
```

And define parameters as follows:
```
title Arch Linux
linux /vmlinuz-linux
initrd /initramfs-linux.img
options root=UUID="[root partition UUID]" rw
```

```title``` is what the boot option will display.\
```linux``` refers to the Linux kernel.\
```initrd``` is what your RAM will be formatted to on boot, so memory can be stored on to it during uptime.\
```options``` specifies your ROOT partition, and specifies that it can be read and written to.

You can find the root partition's UUID by typing into the command line (not your editor):
```
blkid /dev/sdx3
```

(Keeping in mind that sdx refers to the drive you want to install Arch Linux onto)

Save and exit.

We need to make a similar file for the fallback image. To do that, type:
```
cp /boot/loader/entries/arch.conf /boot/loader/entries/arch-fb.conf
```

Edit the file:
```
nano /boot/loader/entries/arch-fb.conf
```

Change the below lines:
```
title Arch Linux
initrd /initramfs-linux.img
```

To (respectively):
```
title Arch Linux Fallback
initrd /initrams-linux-fallback.img
```

Do the same for any other Linux derivatives you have installed.\
Example:
```
title Arch Linux Zen
linux /vmlinuz-linux-zen
initrd /initramfs-linuz-zen.img
options root=UUID="[root partition UUID]" rw
```

**❓: - Did you follow the above steps EXACTLY? Any mistakes made can and will cause your Arch system to fail its boot sequence.** 

</details>

<details>
 <summary><h4>Enable secure boot (optional)<a name="enable-secure-boot"></a></h4></summary>

Secure Boot is a security standard designed to ensure that a device boots using only trusted software. To enable it, go into your firmware settings and erase all of your secure boot keys. The way on how to do so differs between manufacturers.\
Once you've done that, install sbctl by running the below command:

```
sudo pacman -S sbctl
```

Then create secure boot keys by running the below command:
```
sudo sbctl create-keys
```

Then enroll those keys, alongside Microsoft's, to the UEFI
```
sudo enroll-keys -m
```

Check what files need to be signed by running:
```
sbctl verify
```

And sign those files. If you haven't unified your kernel images, sign your boot files as shown below:
```
sudo sbctl sign -s /boot/vmlinuz-linux
sudo sbctl sign -s /boot/EFI/BOOT/BOOTX64.EFI
```

And if you have, sign them as shown below:
```
sudo sbctl sign -s -o /usr/lib/systemd/boot/efi/systemd-bootx64.efi.signed /usr/lib/systemd/boot/efi/systemd-bootx64.efi
sudo sbctl sign -s /efi/EFI/BOOT/BOOTX64.EFI
sudo sbctl sign -s /efi/EFI/Linux/arch-linux.efi
sudo sbctl sign -s /efi/EFI/Linux/arch-linux-fallback.efi
```

If you have any forks of the linux kernel installed (e.g. Linux Zen, Linux Hardened .etc), go ahead and sign those too.

</details>

---

### Create a user account <a name="create-users"></a>

Doing this is common sense, as many greeters do not include the root account as one of the users when logging it.\
To do this, run the below:
```
useradd -mG wheel [username]
```
replacing ```[username]``` with the username of your choice.
The ```-m``` option creates a user directory for you, where local configurations and apps are stored.
The ```-G wheel``` option creates the user as part of the wheel group, which will allow you to make changes on the system itself. For example, downloading and installing packages, and configuring them.

Set a password for the user account by running:
```
passwd [username]
```

Repeat the above process as many times as you want, depending on the amount of users you want to add to your system.\
If you do not want a user to use sudo commands , use the below command instead:

```
useradd -m [username]
```

### Allow Wheel Group to use Sudo Commands <a name="allow-sudo-commands"></a>

You need to do this to actually allow your user to make changes to the system.
```
EDITOR=nano visudo
```

And find and uncomment the below line.
```
#%wheel ALL=(ALL) ALL
```
Save & exit.

### Final Step
```
exit
reboot
```
</br>

---

## Login as your user account <a name="reconnect-to-the-internet"></a>

If you've encrypted your ROOT/HOME partition(s), enter your password to access your Arch system.\
You'll have to reconnect to the internet as we're using network-manager instead of iwd, so our connection settings have been lost and you'll have to reconnect.\
If you were connected to the internet through a Mobile Broadband Modem, the connection process is the same as before.

Firstly, to take a look at what network stations you have installed on your computer, use the command:
```
nmcli device
```
Then, turn on wifi by using the command:
```
nmcli radio wifi on
```
And list local access points by using the command:
```
nmcli device wifi list
```
Select one of the access points listed and connect to it by running the following command:
```
nmcli device wifi connect [Access Point SSID] password [Access Point Password]
```

You won't need to check for updates as Arch will have already downloaded the latest version of Arch Linux.
You can stop here if you want to have a desktop-less Arch system for any reason (this could be for a server install).

---

### Display protocols & GPU Drivers <a name="xorg--gpu-drivers"></a>

#### Display protocols

```X.org``` is the free and open-source implementation of the X Window System (X11) display protocol. However, it is quite old, and it shows. It has multiple issues with it, for example, applications can eavesdrop on each other, animations are laggier and people with multiple monitors or high-density displays (HiDPI) might experience issues.

```Wayland``` is a more recent display protocol. Like X.org, it is free and open-source. However, it is much newer, and more efficient. It offers better security, and modern HiDPI and multi-monitor support. However, it breaks compatibility with certain applications, and although there are compatibility layers such as xorg-xwayland and qt5-wayland, they aren't perfect.

Pick the display protocol of your choice, and install it by clicking on the headers of either of the collapsable sections below.

<details>
 <summary><h3>X.org</h3></summary>
 
 To install X.org, run the below command:
 ```
 sudo pacman -S xorg
 ```
</details>

<details>
 <summary><h3>Wayland</h3></summary>
 
To install Wayland, run the below command:
```
sudo pacman -S --needed wayland xorg-xwayland qt5-wayland qt6-wayland xorg-xlsclients
```

```xorg-xwayland``` is a compatibility layer that allows you to run X11 applications on Wayland.\
```qt5-wayland``` allows you to run Qt5 apps on Wayland.\
```qt6-wayland``` allows you to run Qt6 apps on Wayland.\
```xorg-xlsclients``` lists any apps running on the ```xorg-xwayland``` compatibility layer running on the current display.

</details>

#### GPU drivers

GPU drivers allow your computer to utilise your GPU and massively increase performance during games, if you have a good GPU that is.\
Install your GPU drivers as shown below:

```
sudo pacman -S [GPU driver]
```

- For Nvidia GPUs, type `nvidia` & `nvidia-settings`. For more info/old GPUs, refer to [Arch Wiki - Nvidia](https://wiki.archlinux.org/index.php/NVIDIA).
- For newer AMD GPUs, type `xf86-video-amdgpu`.
- For legacy Radeon GPUs like HD 7xxx Series & below, type `xf86-video-ati`.
- For dedicated Intel Graphics, type `xf86-video-intel`.

### Enable Multilib Repo (optional but absolutely recommended) <a name="multilib"></a>
Multilib allows you to download and run 32-bit software if you're on a 64-bit machine. Of course, if you're running this on a 32-bit machine, you can go ahead and skip this step.

Edit `/etc/pacman.conf` & uncomment the below two lines.
```
#[multilib]
#Include = /etc/pacman.d/mirrorlist
```

### Integrating the AUR with pacman (optional but a good quality-of-life feature)

> Note: It's better to use this method over yay as the packages are pre-built binaries (in simpler terms, it's faster to download)

Chaotic-AUR is a method for retrieving packages in the AUR (packages made by the community that aren't any of the official repos, which are core, extra and multilib). It's better to use the Chaotic-AUR than other AUR helpers as the packages are pre-built binaries, meaning, your computer doesn't have to take its sweet time to create an app from the source code, as is common with helpers such as yay or paru. This is recommended if you're impatient and/or have a slow PC

Retrieve the keyring for the Chaotic-AUR from the Ubuntu keyserver:
```
sudo pacman-key --recv-key 3056513887B78AEB --keyserver keyserver.ubuntu.com
```

Sign it locally:
```
sudo pacman-key --lsign-key 3056513887B78AEB
```

Then download both the keyring and mirrorlist:
```
sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-keyring.pkg.tar.zst'
sudo pacman -U 'https://cdn-mirror.chaotic.cx/chaotic-aur/chaotic-mirrorlist.pkg.tar.zst'
```

And synchronise your computer with both multilib and Chaotic-AUR:
```
sudo pacman -Syy
```

<details>
 <summary><h4>Use powerpill to download from all mirrors simultaneously (optional and highly recommended if you live in a third-world country)</h4></summary>


Download powerpill:
```                                                                                                                                                    
sudo pacman -S powerpill
```

Initialise it as follows:
```
sudo powerpill -Su
```

Then follow the instructions on screen.
</details>


### Install & Enable A Login Manager <a name="install--enable-dm"></a>

If you want to use GNOME as your login manager, install and enable GDM as shown below:
```
sudo pacman -S gdm
sudo systemctl enable gdm.service
```

If you want to use any other window manager, install and enable SDDM as shown below:
```
sudo pacman -S sddm
sudo systemctl enable sddm
```

### Installing a desktop environment <a name="installing-de"></a>

A desktop environment, at its most simplest form, just arranges how your apps and other graphical UI elements are arranged on the screen. Some are more customisable than others.\
There are two main types of Desktop Environments: stacking and tiling.\
Stacking window managers are just your vanilla desktop environment. You use your mouse to stack and arrange windows on your screen.\
Tiling window managers, however, have a steeper learning curve than stacking window managers. You use your keyboard to tile and arrange windows on your screen.

Use a tiling window manager if you use vim, neovim or emacs. Use a stacking window manager if you use nano.\
For a list of notable window managers and how to install them, go to ```window-managers.md```, by clicking [here](https://github.com/Exvix/arch-install-guide/blob/main/window-managers.md)

### Audio Utilities & Bluetooth (optional but recommended) <a name="audio-utilities--bluetooth"></a>
```
sudo pacman -S alsa-utils bluez bluez-utils pipewire wireplumber pipewire-alsa pipewire-pulse
```
Packages       | Description
-------------- | -----------------------------------------
alsa-utils     | This contains (among other utilities) the `alsamixer` and `amixer` utilities.
bluez          | Provides the Bluetooth protocol stack.
bluez-utils    | Provides the `bluetoothctl` utility.
pipewire       | Low-latency software for multimedia processing and sharing
wireplumbler   | Policy and session manager for pipewire
pipewire-alsa  | Provides soundcard drivers
pipewire-pulse | A general sound server intended as a middle-man between your soundcard and applications.

#### Enable Bluetooth
```
sudo systemctl enable bluetooth.service
```

### Final Reboot
```
reboot
```

## Are you dual-booting Arch Linux and Windows? <a name="add-windows-bootmgr"></a>

If so, you still have more steps to complete, you need to add the Windows Boot Manager as one of the boot options to GRUB.\
To do so, go into your system's UEFI firmware interface and move `GRUB` above the Windows Boot Manager, which differs depending on manufacturer.

Install OS-Prober:
```
sudo pacman -S os-prober
```

Uncomment the below line:
```
#GRUB_DISABLE_OS_PROBER=FALSE
```
Save and exit.

Then recreate your `/boot/grub/grub.cfg` file as shown below:
```
sudo grub-mkconfig -o /boot/grub/grub.cfg
```
</br>

---
# The Conclusion <a name="the-conclusion"></a>
Now everything is installed and after the final `reboot`, you will land in the SDDM greeter. You can continue reading for some steps to further improve your experience. I recommend you to install `yay` & `paccache`.
- Yay will provide your packages from AUR (Arch User Repository), which are not available in the official repos.
- Paccache can be used clean pacman cached packages either manually or in an automated way.
</br>

## Extras <a name="extras"></a>

### Apps I would personally recommend installing but aren't required <a name="misc-applications"></a>
You can install all the following packages or only the one you want.
```
sudo pacman -S firefox openssh qbittorrent audacious wget screen git fastfetch lib32-mesa
```
Packages    | Description
----------- | ----------
firefox     | Mozilla Firefox Web Browser.
openssh     | Secure Shell access server, allows you to access remote computers or servers.
qbittorrent | Qt-based BitTorrent Client.
audacious   | Qt-based music player.
wget\*      | Wget is a free utility for non-interactive download of files from the Web. 
screen      | Is a full-screen window manager that multiplexes a physical terminal between several processes, typically interactive shells.
git\*       | Github command-line utility tools. (needed to access the AUR) 
fastfetch   | Fastfetch is a command-line system information tool, that is the sucessor to NeoFetch.
cups\*      | Printer service
lib32-mesa\*| Optional dependency for Steam game using the Vulkan backend 


> \* - These are some of the more important packages, which a lot of programs tend to use. They're optional but it is highly recommended to install both of them.

### Enable OpenSSH daemon and CUPS printer service
```
sudo systemctl enable sshd.service
sudo systemctl enable --now cups.service
```

### Install [Yay](https://github.com/Jguer/yay)
Yet Another Yogurt - An AUR Helper - install this if you don't want to integrate the AUR into pacman.
A lot of programs written for Arch can be founded in the AUR, but be careful of what you download from there.
```
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
```
The below commands delete the yay folder as it isn't necessary anymore
```
cd ..
sudo rm -rf yay
```

### Alternative shells

The shell that comes with your system automatically is bash. However, bash isn't very configurable. To fix that, you can use an alternative shell. Some will be listed below:

Shell   | Description
------- | ------------
zsh     | It is fully backwards compatible with bash, and is highly configurable shell. However, it has quite a learning curve.
fish    | An easy and highly configurable shell. It has features like autosuggestions out of the box. However, if you're used to bash or zsh, it might take some time to learn fish.
nushell | An incredibly fast shell that centers around using objects and modules. It lacks a lot of features that bash, zsh and fish have, however.
----------------------

To take a look at what shell you have as default (this will likely be bash), run the below command:
```
echo $SHELL
```

To see what shells you have installed on your system, run the below command:
```
chsh -l
```

Find the shell of your preference within that list and change it accordingly using the below command:
```
chsh -s [shell]
```

> Replacing ```[shell]``` with the shell of your preference. An example can be found below.

```
chsh -s /bin/zsh
```

### Aptpac

> Note: You should only use this if you're new to Arch Linux or have jumped ship from Debian or its derivatives (like Ubuntu) to Arch.

Install cmake:
```
sudo pacman -S cmake
```

Download the aptpac source code:
```
git clone https://github.com/Itai-Nelken/aptpac
```

Change directory into it:
```
cd ./aptpac/C-edition
```

Make the ```build``` directory, so it can be built and run, then change directory into it:
```
mkdir build && cd build
```

Then build the source code:
```
cmake -DCMAKE_BUILD_TYPE=Release ..
make
```

And finally, move it to ```/usr/local/bin``` so it can be run easily as a command:
```
sudo make install
```

### Replacing sudo with doas

Sudo has many issues with it. Firstly, it has a lot of features that only IT administrators would use (that many would consider bloat), and it has quite an old and messy codebase, leading it vulnerable to cybercriminals to attack your system, and is definitely no stranger to having bugs that have existed for over 5 years.\
If the only thing you ever use sudo for is for making changes to your system, such as `sudo pacman` or `sudo systemctl`, then it would make more sense to use `doas`.

Opendoas is an implentation of doas for Linux. Install it, as shown below:
```
sudo pacman -S opendoas
```

Then, allow any member of the `wheel` group to run `doas` commands, as shown below:
```
sudo echo 'permit :wheel' >> /etc/doas.conf
```

And change its owner and permissions for the sake of cybersecurity:
```
sudo chown -c root:root /etc/doas.conf
sudo chmod -c 0400 /etc/doas.conf
```

Now you're ready to use doas on your system. Its configuration and usage is similar to that of `sudo`, but do go and check its manpage.
```
man doas.conf # For configuration
man doas # For usage
```

## Maintenance, Performance Tuning & Monitoring

### Paccache
Pacman Cache Cleaner.

Install
```
sudo pacman -S pacman-contrib
```

To manually clean pacman cache, run
```
sudo paccache -rk
```
Where, *k* indicates to keep "num" of each package in the cache.

#### To automate paccache process

Create a file in `/etc/pacman.d/hooks` by running the below 
```
sudo mkdir /etc/pacman.d/hooks
sudo nano /etc/pacman.d/hooks/clean_cache.hook
```

Append the following lines to it
```
[Trigger]
Operation = Upgrade
Operation = Install
Operation = Remove
Type = Package
Target = *
[Action]
Description = Cleaning pacman cache...
When = PostTransaction
Exec = /usr/bin/paccache -rk
```
Save & exit.

### Install [Cockpit](https://cockpit-project.org/)
A systemd web based user interface for Linux servers, workstations and even desktops. It can be used to monitor your system stats, performance and perform various settings including updating your system.
```
sudo pacman -S cockpit
```

##### Enable Cockpit
```
sudo systemctl enable --now cockpit.socket
```
Now open your browser and point to it `your-machine-ip:9000` and login with ***root*** to get full access.

---

## Latest changes 

 - **2024-08-30 patch 1**
   - Added section for `doas`
   - Moved `sbctl` section out of the `SystemD-Boot` section
   - Minor rewordings

**Full Changelog**: https://github.com/Exvix/arch-install-guide/releases
