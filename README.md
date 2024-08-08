> Tested working as of: 2024.08.01

# The ultimate Arch linux install guide (UEFI & MBR)

## Table of Contents
 - [**Introduction**](#introduction)
 - [**Booting into the ArchISO**](#boot-to-archiso)
   - [**Creating the installation medium**](#creating-boot-medium)
   - [**Entering your computer's boot menu**](#entering-boot-menu)
 - [**Connect to the Internet**](#connect-to-the-internet)
 - [**Disk Partitioning**](#partition-disk)
 - [**Base System Installation**](#base-system-installation)
   - [Update Mirrors](#update-mirrors-using-reflector)
   - [Base System](https://github.com/XxAcielxX/arch-plasma-install#install-base-system)
   - [Generate fstab](#generate-fstab)
 - [**Chroot**](#chroot)
   - [Date & Time](#set-time--date)
   - [Language](#set-language)
   - [Hostname & Hosts](#set-hostname)
   - [Network Manager](#install--enable-networkmanager)
   - [ROOT Password](#set-root-password)
   - [Installing the Bootloader](#installing-bootloader)
     - [GRUB](#grub)
     - [SystemD-Boot](#systemd-boot)
     - [Making and editing config files (UEFI ONLY)](#making-config-file)
 - [**Boot Freshly Installed System**](#now-boot-into-your-freshly-installed-arch-system)
   - [Connect to the internet (again)](#reconnect-to-the-internet)
   - [Add User](#add-new-user)
   - [Sudo Command](#allow-wheel-group-to-use-sudo-commands)
 - [**User Login**](#reconnect-to-the-internet)
   - [Display Server & GPU Drivers](#xorg--gpu-drivers)
   - [Multilib Repository (32bit)](#multilib)
   - [Display Manager (SDDM)](#install--enable-sddm)
   - [Desktop Environment (KDE Plasma)](#kde-plasma--applications)
   - [Audio Utilities & Bluetooth](#audio-utilities--bluetooth)
   - [Misc Applications](https://#my-required-applications)
 - [**The Conclusion**](#the-conclusion)
 - [**Extras (optional)**](#extras)
   - [Yay](#install-yay)
   - [Alternative Shells](#alternative-shells)
   - [Change SHELL](#changing-your-shell)
   - [PipeWire](#pipewire)
   - [EasyEffects](#easyeffects)
   - [Clam AntiVirus](#clamav)
 - [**Theming & Customisations**](#theming--customisations)
    - [Oh My Zsh & Powerlevel10k Theme](#install-oh-my-zsh)
    - [Kvantum Manager](#kvantum-manager)
 - [**Maintenance, Performance Tuning & Monitoring**](maintenance-performance-tuning--monitoring)
   - [Paccache](#paccache)
   - [Cockpit](#install-cockpit)
 - [**Changelog**](#changelog)
</br>

# Introduction

## What is Arch Linux?
Arch Linux is an independently-developed rolling-release distribution of Linux. This means that there are no major updates that are delivered to Arch, unlike Windows. Instead, Arch delivers updates in small, frequent amounts, meaning that you have access to the latest drivers, firmware and software as soon as it's available. Since Arch is based off the Linux kernel, it's distributed under the GNU GPLv2 license, meaning that anyone can view the kernel, and what makes up Arch, as well as being able to modify it any point, so long as the software remains open-source.

Arch Linux is fantastic for its Arch User Repository (AUR) and simplicity of design. It is well known for its straightforwardness and is relatively easy for the end user to understand directly, rather than having to dig through multiple layers of graphical interfaces. The package manager - pacman - has no GUI, meaning that everything has to be installed through the command line. This may seem a bit scary at first, but in this guide, I will go through how to install Arch Linux, and how to use its many features.

---

# Booting into the ArchISO <a name="boot-to-archiso"></a>

## Creating the Arch installation medium <a name="creating-boot-medium"></a>

Firstly, get the EFI ISO file from [the official Arch Linux website](https://archlinux.org/download/) or [the website for the community-maintained 32-bit Arch Linux OS](https://www.archlinux32.org/download/) either through torrenting it, or using of the mirrorlinks.\
You'll need to use software such as [Ventoy](https://www.ventoy.net/en/download.html), [Rufus](https://rufus.ie/en/) or [BalenaEtcher](https://etcher.balena.io/#download-etcher). The instructions on how to use such software can be found within their websites.

## Entering the boot menu <a name="entering-boot-menu"></a>

Once the ISO has been written to a storage medium (that could be a USB thumb drive, SD card .etc), you'll need to go into your computers boot options through the firmware interface. To do so, follow the instructions below, based on your Operating System.

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

### Connect to the internet

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
It will ask you if you want to confirm, enter ```y``` and it will then if ask you want to "blank out MBR?", which means it will wipe all of the boot code and other critical data necessary for an OS to boot. Since you're no longer booting from your old OS, go ahead and hit ```y```.\

Run gdisk again and follow the below instructions (do not type anything where ```=``` precedes it, as those are just explanations for what each command does):

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

### Format non-swap partitions (so that data can be written/read from it)
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

### Install base system
```
pacstrap /mnt base base-devel linux linux-firmware linux-headers nano intel-ucode mtools dosfstools networkmanager
```

`base` is all the software necessary to make Arch Linux work.\
`base-devel` is software for development purposes only, such as GCC or G++.\
`linux`, `linux-headers` and `linux-firmware` are the kernel, which, together, act as the middleman between the software on your computer and the hardware on your computer.\
`mtools` allows the user to access MS-DOS disks.\
`dosfstools` allows the user to format MS-DOS disks./
`networkmanager` allows the user to connect to the internet.\

- Replace `nano` with editor of your choice (i.e `vim` or `vi`). Nano is the most beginner friendly
- Replace `intel-ucode` with `amd-ucode` if you are using an AMD Processor.
- If you want to include a derivative of the Linux kernel (e.g. linux-zen, linux-hardened, linux-lts), include that ALONGSIDE the original kernel and headers so if something breaks, you always have that extra option to use the regular Linux kernel. This can be done by appending your chosen Linux derivative (e.g. `linux-zen`) and its headers (e.g. `linux-zen-headers`)

### Generate fstab

The fstab file lists all available disk partitions and other types of file systems and data sources that may not necessarily be disk-based, and indicates how they are to be initialized or otherwise integrated into the larger file system structure. It is *essential* for your system to function.

```
genfstab -U /mnt >> /mnt/etc/fstab
```

> Note: A ```>``` will overwrite a file and a ```>>``` will append to a file. Ensure you don't confuse these with each other, and make sure the commands you type are as how this guide has written it before you hit enter.


---

## Chroot

Chroot essentially takes you inside of the Arch Linux system, so you can perform necessary configuration to it for it to function. This can be done by running:

```
arch-chroot /mnt
```

### Set Time & Date

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

## Set Language
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
save & exit, by hitting Ctrl+W, Enter then Ctrl+X.

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

## Set Hostname

A hostname is what your computer calls itself when talking to other computers. To set a hostname, run the below command:

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
Replace `arch` with hostname of your choice.\
Save & exit.

### Enable NetworkManager
```
systemctl enable NetworkManager
```

### Set ROOT Password

The ROOT account is an account on your computer that can do anything on your computer. Set a password for it as not doing so leaves your system vulnerable to cyberattackers.

```
passwd
```

## Installling the bootloader <a name="installing-bootloader"></a>

The bootloader is what manages the boot process, and is the PID 0 of your Arch system.\
If you're on an MBR systems, install GRUB and if you're on an EFI system, install SystemD-Boot.

<details>
 <summary><h3>Installing and generating config files for GRUB</h3></summary>
"Targets" are CPU architechtures. These are important for grub to know so it can handle the boot proess correctly.\
Find your CPU architechture from [this site](https://renenyffenegger.ch/notes/Linux/shell/commands/grub-install#grub-install-target) and specify that as the target.

```
pacman -S grub
grub-install /dev/[disk name] # You don't need to specify a target because the default is i386-pc
grub-mkconfig -o /boot/grub/grub.cfg
```

</details>

<details>
 <summary><h3>Installing and configuring SystemD-Boot (UEFI)</h3></summary>


Install SystemD-Boot by running:
```
bootctl install
```

#### Creating and amending config files <a name="making-config-file"></a>

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

```timeout 3``` stalls your system by 3 seconds before it boots so you have time to select an option.\
```console-mode keep``` keeps your console-based SystemD-Boot menu to how it was last time.\
```default arch.conf``` is what SystemD-Boot automatically boots to when you don't select an option.\
```editor no``` prevents any edits to be made to the boot options within the menu itself.

Once that's done, type:
```
nano /boot/loader/entries/arch.conf
```

And define parameters as follows:
```
title    Arch Linux
linux    /vmlinuz-linux
initrd   /initramfs-linux.img
options  root=UUID="[root partition UUID]" rw
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

**:warning: - Did you follow the above steps EXACTLY? Any mistakes made can and will cause your Arch system to fail its boot sequence.** 

</details>

### Final Step
```
exit
reboot
```
</br>

## Now boot into your freshly installed Arch system

### Login as "root" and enter root password when prompted

### Add new User
```
useradd -mG wheel [username]
```
Replace `[username]` with your username of choice.

### Set User Password
```
passwd [username]
```

Repeat the above process as many times as you want, depending on the amount of users you want to add to your system.\
If you do not want a user to use sudo commands, use the below command instead:

```
useradd -m [username]
```

### Allow Wheel Group to use Sudo Commands
```
EDITOR=nano visudo
```

#### Find and uncomment the below line
```
#%wheel ALL=(ALL) ALL
```
save & exit.

### Logout of "root"
```
exit
```

## Login as USER and let's connect to the internet again! <a name="reconnect-to-the-internet"></a>

Since we're now using NetworkManager instead of iwd, our connection settings have been lost (and the connection process is slightly different)\
Firstly, to take a look at what network stations you have installed on your computer, use the command:
```
nmcli device
```
Then, we turn on wifi by using the command:
```
nmcli radio wifi on
```
And we list local access points by using the command:
```
nmcli device wifi list
```
Select one of the access points listed and connect to it by running the following command:
```
nmcli device wifi connect [Access Point SSID] password [Access Point Password]
```

You don't need to check for updates as Arch will have already downloaded the latest version of Arch Linux

You can stop here if you want to do a server installation or have a desktop-less Arch system for any other reason.

### Xorg & GPU Drivers
```
sudo pacman -S xorg [xf86-video-your gpu type]
```
- For Nvidia GPUs, type `nvidia` & `nvidia-settings`. For more info/old GPUs, refer to [Arch Wiki - Nvidia](https://wiki.archlinux.org/index.php/NVIDIA).
- For newer AMD GPUs, type `xf86-video-amdgpu`.
- For legacy Radeon GPUs like HD 7xxx Series & below, type `xf86-video-ati`.
- For dedicated Intel Graphics, type `xf86-video-intel`.

### Enable Multilib Repo (optional but absolutely recommended) <a name="multilib"></a>
multilib contains 32-bit software and libraries that can be used to run and build 32-bit applications on 64-bit installs (e.g. [Wine](https://www.winehq.org/), [Steam](https://store.steampowered.com/), etc).

Edit `/etc/pacman.conf` & uncomment the below two lines.
```
#[multilib]
#Include = /etc/pacman.d/mirrorlist
```

#### MESA Libraries (32bit) (optional but highly recommmended)
This package is required by Steam if you play games using Vulkan Backend.
```
sudo pacman -Sy lib32-mesa
```

Note: The above install will not work if you don't specify ```-Sy``` or type ```sudo pacman -Syy``` beforehand.

### Install & Enable SDDM
```
sudo pacman -S sddm
sudo systemctl enable sddm
```

### KDE Plasma & Applications
```
sudo pacman -S plasma konsole dolphin ark kwrite kcalc spectacle krunner partitionmanager packagekit-qt5
```
Packages         | Description
---------------- | ------------------------------------
plasma           | KDE Plasma Desktop Environment.
konsole          | KDE Terminal.
dolphin          | KDE File Manager.
ark              | Archiving Tool.
kwrite           | Text Editor.
kcalc            | Scientific Calculator.
spectacle        | KDE screenshot capture utility.
krunner          | KDE Quick drop-down desktop search.
partitionmanager | KDE Disk & Partion Manager.

### Audio Utilities & Bluetooth (optional but recommended) <a name="audio-utilities--bluetooth"></a>
```
sudo pacman -S alsa-utils bluez bluez-utils
```
Packages    | Description
----------- | -----------------------------------------
alsa-utils  | This contains (among other utilities) the `alsamixer` and `amixer` utilities.
bluez       | Provides the Bluetooth protocol stack.
bluez-utils | Provides the `bluetoothctl` utility.

#### Enable Bluetooth Service
```
sudo systemctl enable bluetooth.service
```


### Apps I would personally recommend installing but aren't required
You can install all the following packages or only the one you want.
```
sudo pacman -S firefox openssh qbittorrent audacious wget screen git neofetch
```
Packages | Description
--------- | ----------
firefox | Mozilla Firefox Web Browser.
openssh | Secure Shell access server.
qbittorrent | Qt-based BitTorrent Client.
audacious | Qt-based music player.
wget\* | Wget is a free utility for non-interactive download of files from the Web. 
screen | Is a full-screen window manager that multiplexes a physical terminal between several processes, typically interactive shells.
git\*| Github command-line utility tools. (needed to access the AUR) 
fastfetch | Fastfetch is a command-line system information tool, that is the sucessor to NeoFetch.
cups\*| Printer service

> \* - These are some of the more important packages, which a lot of programs tend to use. They're optional but it is highly recommended to install both of them.

### Enable OpenSSH daemon and CUPS printer service
```
sudo systemctl enable sshd.service
sudo systemctl enable --now cups.service
```

### Final Reboot
```
reboot
```
</br>

### The Conclusion <a name="the-conclusion"></a>
Now everything is installed and after the final `reboot`, you will land in the SDDM greeter. You can continue reading for some steps to further improve your experience. I recommend you to install `yay` & `paccache`.
- Yay will provide your packages from AUR (Arch User Repository), which are not available in the official repos.
- Paccache can be used clean pacman cached packages either manually or in an automated way.
</br>

## Extras (optional but worth a read) <a name="extras"></a>

### Install [Yay](https://github.com/Jguer/yay)
Yet Another Yogurt - An AUR Helper.
A lot of programs written for Arch can be founded in the AUR, but be careful of what you download from there.
```
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
cd .
rm -rf yay # To delete the yay folder as it isn't necessary anymore
```

### Install [NuShell](https://www.nushell.sh) <a name="alternative-shells"></a>
NuShell is a powerful shell that has really helpful debug statements and is overall a solid shell environment.
```
yay -S nushell
```

### Install [Zsh](https://wiki.archlinux.org/index.php/zsh/)
Zsh is a powerful shell that operates as both an interactive shell and as a scripting language interpreter. It's a preferred shell environment by many.
```
sudo pacman -S zsh zsh-completions
```
Read *[here](#install-oh-my-zsh)* for customisation & theming for Zsh. Read below how to change your SHELL.

### Changing your SHELL
First check your current SHELL by running:
```
echo $SHELL
```

#### To get list of all available/installed SHELLs:
```
chsh -l
```

### Set NuShell or Zsh as our SHELL
For an example, we will set Zsh as default SHELL which we installed in the last step:
```
chsh -s /usr/bin/zsh # To set Zsh as the default SHELL
chsh -s /usr/bin/nu # To set NuShell as the default SHELL
```
For the changes to apply, you will have Logout and Log back in or better do `reboot`.

## PipeWire
[PipeWire](https://wiki.archlinux.org/title/PipeWire) is a new low-level multimedia framework. It aims to offer capture and playback for both audio and video with minimal latency and support for PulseAudio, JACK, ALSA and GStreamer-based applications.
#### Install
```
sudo pacman -S pipewire wireplumber pipewire-audio pipewire-alsa pipewire-pulse
```

## EasyEffects
[EasyEffects](https://wiki.archlinux.org/title/PipeWire#EasyEffects) (former PulseEffects) is a GTK utility which provides a large array of audio effects and filters to individual application output streams and microphone input streams. Notable effects include an input/output equalizer, output loudness equalization and bass enhancement, and input de-esser and noise reduction plug-in.
Install
```
sudo pacman -S easyeffects
# or
yay -S easyeffects-git
```
> This will also install pipewire-pulse and replace PulseAudio with PipeWire.

## ClamAV
[Clam AntiVirus](https://wiki.archlinux.org/index.php/ClamAV) is an open source (GPL) anti-virus toolkit for UNIX. It provides a number of utilities including a flexible and scalable multi-threaded daemon, a command line scanner and advanced tool for automatic database updates.
#### 1. Install
```
sudo pacman -S clamav
```

#### 2. Update Signatures/Database (must do)
```
sudo freshclam
```

#### 3. Enable & start services
```
sudo systemctl enable --now clamav-freshclam.service
sudo systemctl enable --now clamav-daemon.service
```

#### 4a. ClamTK (optional)
GUI for ClamAV
```
sudo pacman -S clamtk
```

#### 4b. KDE Dolphin File Manager Plugin (optional)
Download the latest `master zip` from [ClanTK-KDE Gitlab](https://gitlab.com/dave_m/clamtk-kde) & extract it your `~/Downloads` folder. Now open a terminal from within the extracted folder & run:
```
sudo cp clamtk-kde.desktop /usr/share/kservices5/ServiceMenus/
```
</br>

## Theming & Customisations

### Install [Oh My Zsh](https://ohmyz.sh/)
Oh My Zsh is an open source, community-driven framework for managing your Zsh configuration.
```
sh -c "$(wget https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh -O -)"
```
My favourite theme is Powerlevel10k (follow below for installation).
- You can visit [here](https://github.com/ohmyzsh/ohmyzsh/wiki/Themes) to download theme of your choice.

### Get [Powerlevel10k](https://github.com/romkatv/powerlevel10k/) Theme for Oh My Zsh
This is the theme I'll install to spice up my terminal experience.
```
git clone --depth=1 https://github.com/romkatv/powerlevel10k.git ${ZSH_CUSTOM:-$HOME/.oh-my-zsh/custom}/themes/powerlevel10k
```

#### Get the recommended fonts
We will be using ***Yay*** to install the below two packages as one of them is only available from AUR.
```
yay -S ttf-dejavu ttf-meslo-nerd-font-powerlevel10k
```
Also set your Konsole Terminal font to `MesloGS-NF-Regular`.

#### Set Powerlevel10k as your Zsh Theme
```
nano ~/.zshrc
```
Find the line starting with `ZSH_THEME="...."` and replace the theme name so the line should now look like this `ZSH_THEME="powerlevel10k/powerlevel10k"` Now do `source ~/.zshrc`.
#### Configuration
> ***For new users***, on the first run, Powerlevel10k configuration wizard will ask you a few questions and configure your prompt. If it doesn't trigger automatically, type `p10k configure`. Configuration wizard creates `~/.p10k.zsh` based on your preferences. Additional prompt customization can be done by editing this file. It has plenty of comments to help you navigate through configuration options.

## Kvantum Manager
[Kvantum](https://github.com/tsujan/Kvantum) is a SVG-based theme engine for Qt, tuned to KDE and LXQt, with an emphasis on elegance, usability and practicality.

### Install through Yay (git version)
```
yay -S kvantum-qt5-git
```

***Or***

### Install through Pacman
```
sudo pacman -S kvantum
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

Create a file in `/etc/pacman.d/hooks`
```
sudo mkdir /etc/pacman.d/hooks
sudo nano /etc/pacman.d/hooks/clean_cache.hook
```

Add the following lines in it
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
save & exit.

### Install [Cockpit](https://cockpit-project.org/)
A systemd web based user interface for Linux servers, Workstations and even Desktops. Can be used to monitor your system stats, performance and perform various settings including updating of your system.
```
sudo pacman -S cockpit
```

##### Enable Cockpit
```
sudo systemctl enable --now cockpit.socket
```
Now open your browser and point to it `your-machine-ip:9000` and login with ***root*** to get full access.

</br>

## Changelog

 - **2024-08-08**
   - Initial guide created. 

#### Full and complete changelog, [click here]([https://github.com/Exvix/arch-install-guide/blob/main/CHANGELOG.md].
