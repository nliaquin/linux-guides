# Installing Arch Linux, Manually, Fast, and Efficiently
### Last Updated: November 6th, 2021

Many new Linux users look at Arch as one of the final bosses of distro hopping, and some even outright say it's one of the hardest distros to install, but it's actually not! Some have opted to use the recently released guided installer, which is just a python script that comes with the image, but it occasionally flubs up and leaves you with issues. In this guide, I'm going to show you how to manually install Arch Linux fast and efficiently with some sections that even the installaion wiki doesn't tell you about.

### Table of Contents
- [Preliminaries before starting](#preliminaries-before-starting)
- [Checking the EFI Status](#checking-the-efi-status)
- [Updating the System Clock](#updating-the-system-clock)
- [Partitioning Your Hard Drive](#partitioning-your-hard-drive)
- [Mounting the Partitions](#mounting-the-partitions)
- [Setting Pacman Mirrors](#setting-pacman-mirrors)
- [Pacstrapping](#pacstrapping)
- [Generating fstab](#generating-fstab)
- [Chrooting In](#chrooting-in)
- [Setting the Timezone](#setting-the-timezone)
- [Configuring Localization](#configuring-localization)
- [Configuring Networking](#configuring-networking)
- [Setting Root Password](#setting-root-password)
- [Installing and Configuring GRUB](#installing-and-configuring-grub)
- [Post-Installation](#post-installation)


### Preliminaries before starting
There are a number of things you might need to address before getting started:

 - Connecting to wifi
If you are using a laptop and do not with to connect to the internet with ethernet during this installation, you have a could viable options. I have a couple of guides that teach you how to quickly connect to wifi using either [iwd](https://github.com/nliaquin/linux-guides/blob/main/networking/iwd.md) or [wpa_supplicant](https://github.com/nliaquin/linux-guides/blob/main/networking/wpa_supplicant.md). For a temporary connection, I recommend the wpa_supplicant guide, but iwd is very fast and efficient and could be better for saving you time.

 - Controlling your laptop backlight before starting
If you're on a laptop, chances are that some power-saving module may have automatically turned your brightness down to its lowest. This can happen in a number of situations, but I highly recommend you check out my [control-laptop-backlight.md](https://github.com/nliaquin/linux-guides/blob/main/control-laptop-backlight.md) guide so you can adjust the laptop screen brightness for a more comfortable experience installing Arch.

 - Disabling the pcspkr module, aka LOUD BEEP
You're going to notice that, while installing Arch, there is a loud beep whenever you backspace in the shell with no text in the buffer, or when you try to auto-fill your shell but the interpreter came up with nothing. Visit my [disabling-pcspkr.md](https://github.com/nliaquin/linux-guides/blob/main/arch/disabling-pcspkr.md) guide to learn how you can both temporarily and permanently turn it off.


### Checking the EFI Status
All modern computers are EFI-boot, but you might be working with an older motherboard, so you'll want to make sure you know whether or not your computer is using EFI.

To check, enter:
```bash 
ls /sys/firmware/efi/efivars
```

One of two outcomes occur. Either you'll see a whole bunch of output, an error, or nothing at all. If you see a whole bunch of output on the screen, that means you have efi vars, and thus, efi. If no output is given, or an error saying the direcory does not exist, this means you have BIOS-boot. This does impact two of the steps later in the installation, so take careful note as to what the output was.


### Updating the System Clock
If the system clock is innacurate, there will be complications regarding the package manager. Enter the following to ensure that your system clock will be accurate during installation:
```bash 
timedatectl set-ntp true
```


### Partitioning Your Hard Drive
I'm not going to cover how to partition here, but you can check out my [partitioning.md](https://github.com/nliaquin/linux-guides/blob/main/partitioning.md) guide which goes over partitioning in thorough detail for multiple scenarios, including installing Linux. I'm just going to suggest the partition schema you should go with here:

 - boot partition       512M             fat32
 - root partition       25G to 50G       ext4
 - swap partition       4G to 8G
 - home partition       remainder        ext4

Next, lets format these partitions by executing the following commands:
```bash 
mkfs.fat -F32 /dev/sda1
```

```bash 
mkfs.ext4 /dev/sda2
```

```bash 
mkswap /dev/sda3
```

```bash 
mkfs.ext4 /dev/sda4
```

### Mounting the Partitions
Now that we've created the partitions, we have to mount them into the right places. On Arch, /mnt is the location where you'll be setting up Arch for use.

An important thing to remember is that you want to mount your root partition first:
```bash 
mount /dev/sda2 /mnt
```

Before continuing, remember whether *ls /sys/firmware/efi/efivars* produced output. If yes, do the following:
```bash 
mkdir /mnt/boot && mkdir /mnt/boot/efi
```

```bash 
mount /dev/sda1 /mnt/boot/efi
```

If no, do this instead:
```bash 
mkdir /mnt/boot
```

```bash 
mount /dev/sda1 /mnt/boot
```

More on this later...

Then we'll activate the swap partition:
```bash 
swapon /dev/sda3
```

Lastly, we mount home:
```bash 
mkdir /mnt/home
```

```bash 
mount /dev/sda4 /mnt/home
```


### Setting Pacman Mirrors
This is an optional step that can be beneficial in two ways: the first being faster download speeds when installing or updating packages. The second being that if you're on campus wifi, work wifi, or any network that is not your own, meaning you did not configure it, mirrorlists are a potential step to getting around blacklisted sites on the network. If you find yourself unable to download any packages while on a given connection, and you're not using a mirrorlist, I heavily encourage doing this.

First, synchronize the pacman databases:
```bash 
pacman -Syy
```

Once this is done, install git:
```bash 
pacman -S git
```

Use git to download my secure mirror list:
```bash 
git clone https://github.com/nliaquin/arch-mirrors
```

One problem a lot of people have is a message stating *GLIBC_2.33 not found (required by git)*
```bash 
pacman -S glibc
```

That resolves that issue, now rerun my git clone command and it will work.

A quick side-note: If you can't use pacman because you do not have a mirrorlist set up beforehand, you can easily download my mirrorlist from any other computer, put it on a flash drive, and mount that flash drive while installing Arch. Then proceed to do the following.

Simply move the mirrorlist to the following destination:
```bash
mv arch-mirrors/mirrorlist > /etc/pacman.d/
```

I keep this list updated once a month as a convenience. Feel free to use it yourself from now on when it's time to update your mirrors.


### Pacstrapping
Usually during this step, people just install the standard packages, but I think this is actually a good time to install some other essentials like iwd, which does not come with Arch post-installation, dhcpcd, which will help with resolving hosts when you boot in post-installation, and other great utilities:

```bash
pacstrap /mnt base base-devel linux linux-firmware iwd w3m efibootmgr vim git dhcpcd dhclient bash man-db man-pages texinfo openssh polkit sudo brightnessctl lshw neofetch
```

This is a ton of stuff to help you get going, but if you want to know what all of it does, you can look up the package name, or use man *package-name* after finishing installation.


### Generating fstab
In order to boot properly into the system each time we start up, you need to create a table with all of the partition mapping information in /etc/fstab:
```bash
genfstab -U /mnt > /mnt/etc/fstab
```


### Chrooting In
This next step will allow you to modify your base Arch system before you even finish installing it. Basically, you're about to start modifying your distro as if it's already installed before you even run it. Chrooting into Linux has a number of different use-cases, such as system recovery. From this point forward, everything we do directly affects Arch post-installation:
```bash 
arch-chroot /mnt
```


### Setting the Timezone
Let's say you live in New York, you would run the following:
```bash 
ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime
```

If you lived in Detroit, you would have written America/Detroit instead.

Adjust the time to fit that region change:
```bash 
hwclock --systohc
```

### Configuring Localization
Use nano or vim to modify /etc/locale.gen and uncomment out the line down below where it just says the following:
```bash 
#en_US.UTF-8 UTF-8
```

Now initialize the locale file:
```bash 
locale-gen
```

Set the encoding language to, for instance, UTF-8 for US computers:
```bash 
echo "LANG=en_US.UTF-8" > /etc/locale.conf
```

This just ensures that when programs look for your locale, they can default to it instead of C. Most programs will default to C if this isn't done, which is fine, but you'll just keep getting errors every time you run programs looking for the locale.gen value while still doing what you've asked.


### Configuring Networking
Giving your machine a host identity is crucial, as Linux requires a hostname in order for certain networking functions to work properly. Let's say we're naming our machine arch-laptop:
```bash 
echo arch-laptop > /etc/hostname
```

Next, use vim or nano to modify your system's hosts file:
```bash 
vim /etc/hosts
```

Once in editing mode, write out the following, given you went with arch-laptop as your host name:

```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   arch-laptop.localdomain arch-laptop
```

Now you'll want to enable iwd and dhcpcd at startup so that networking just works on boot:
```bash 
systemctl enable iwd
```

```bash 
systemctl enable dhcpcd
```

Lastly, if you had to use rfkill in order to unblock wlan0, run the following command:
```bash 
systemctl enable rfkill-unblock@all
```


### Setting Root Password
Because we need to log into Arch as root post-installation on the first go, you need to set a root password before you forget like so:
```bash 
passwd
```

You'll get a message asking you to set the password. Set it to something good, but don't plan on using root as your main user. Many programs will not allow this in the Linux community.


### Installing and Configuring GRUB
Grub is the bootloader that gives you the ability to actually start Linux after booting. You'll need to first download grub like so:
```bash 
pacman -S grub
```

From here, we can do this two different ways: if *ls /sys/firmware/efi/efivards* produced results, do the following:
```bash 
grub-install --target=x86_64-efi --efi-directory=/boot/efi
```

If not, do this instead:
```bash 
grub-install --target=i386-pc
```

Lastly, export your grub configuration:
```bash 
grub-mkconfig -o /boot/grub/grub.cfg
```

Once that's all finished, exit chroot:
```bash 
exit
```

Lastly, unmount root:
```bash 
umount -R /mnt
```

Now you may reboot:
```bash 
reboot
```

Although, I prefer to shutdown instead, unplug my usb installation medium, and then start back up. A lot of EFI systems tend to try and boot automatically back into a bootable usb stick lately, including newer Thinkpads.


### Post-Installation
A problem you might run into is with internet after finishing the installation.

What to do if dhcp doesn't work on startup:
```bash 
ip link set wlan0 up
```

```bash 
dhclient wlan0
```

You also want to set up another non-root account before you just start ricing, so do the following:
```bash 
useradd -m -G wheel -s /bin/bash *username*
```

If you're wondering, the -m flag is to create a user directory named after the username in /home. The -G flag is for group, and whatever text that follows between the -G flag and the next flag will be the groups this user is in. The wheel group is for users who will have been granted sudo. The -s flag is for which shell this user will have by default. I prefer bash, but you can use whichever you like.

You need to also set a password for the user:
```bash 
passwd *username*
```

You'll be asked to enter a password, and to also confirm the password.

Lastly, we need to go into /etc/sudoers with vim:
```bash 
vim /etc/sudoers
```

This file is long, so type */wheel* and hit enter to find the line that looks like this:
```bash
# wheel ALL=(ALL) ALL
```

and uncomment it by removing the pound symbol. This way, your new user and actually use administrative commands via sudo.

You should now log into this account from now on, and never use root unless it's for system recovery purposes, or if you plan on whiping away all accounts and starting fresh in the system.

Lastly, those [preliminaries](https://github.com/nliaquin/linux-guides/blob/main/arch/0-installing-arch.md#preliminaries-before-starting) you did at the beginning? Well, you'll have to do them again. The thing you might not understand yet is that the live Arch bootcd is where you made those changes, but none of those changes persist until you perform them in your new Arch environment. 

That's everything you needed to know for both installation and post-installation, and if you have any questions, email me via nickolas@nliaquin.xyz
