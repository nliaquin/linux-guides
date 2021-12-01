# Installing Arch Linux, Manually, Fast, and Efficiently
### Last Updated: November 6th, 2021

Many new Linux users look at Arch as one of the final bosses of distro hopping, and some even outright say it's one of the hardest distros to install, but it's actually not! Some have opted to use the recently released guided installer, which is just a python script that comes with the image, but it occasionally flubs up and leaves you with issues. In this guide, I'm going to show you how to manually install Arch Linux fast and efficiently with some sections that even the installaion wiki doesn't tell you about.

### Table of Contents
- [Disable pcspkr Module](#disable-pcspkr-module)
- [Connecting to the internet](#connecting-to-the-internet)
- [Brightness Problem on Laptops](#brightness-problem-on-laptops)
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


### Disable pcspkr Module
First of all, you're going to notice that nearly every time to tab to autocomplete while installing linux, there will be a loud, obnoxious beep. This is called the pcspkr module. Nearly every Arch user disables this right away. To do so, enter the following:

```bash 
rmmod pcspkr
```
At the end of the guide, there will be a small instruction on how to disable this permanently after you finish installing Arch.


### Connecting to the internet
In recent years, the Arch installation image comes with a very easy to use wifi commandline program called **iwd** which does not require as much setup as NetworkManager does.

Start iwd from the commandline with the following command:
```bash 
iwctl
```

You'll be put into a commandline interface, and you'll now want to list your network devices:
```bash
station list
```

At this point, one of two things will occur. Either you'll see a device named something like wlan0, you'll see nothing, or you'll see other devices such as enp2s0. The fact is, we need a wlan device for wifi, and if you don't see wlan0, or something that starts with wlan, you need to exit iwd by either typing exit, or pressing ctrl+c.

If you have a wifi adapter on your device, regardless if it's PCI or USB, you have a wlan device, but it's currently being blocked. To unblock it, type the following:
```bash 
rfkill unblock wifi
```

This was a temporary unblocking of your wlan device. We'll whitelist it permanently later in the guide. For now, type in iwctl again, and then use **station list** once more to see your network interface. If wlan0 shows up, you'll want to scan for networks like so:
```bash 
station wlan0 scan
```

After scanning, you can list your networks using:
```bash 
station wlan0 get-networks
```

If you see your wifi network, or if you already knew your network name, all you have to do is:
```bash 
station wlan0 connect **Network Name**
```

Auto-completion is built into iwd. Typing the first couple of letters/numbers of your wifi network and pressing tab will autofill your networks ssid. You will be prompted to enter the network passphrase, and once you successfully connect to the wifi, you'll get no confirmation. Just exit the program and move forward. You will get a warning if you entered the wrong passphrase.

After exiting iwd, enter the following to test your connection:
```bash 
ping -c3 google.com
```

This just pings 3 times so you don't have to cancel or exit the ping program.


### Brightness Problem on Laptops
You're going to notice the screen is really dark on some laptops. This is because your laptop has given a ctl module a value indicating that the power is too low, or the laptop is unplugged. The default behavior is to respond by lowering backlight brightness to reserve battery. Sometimes this just triggers despite being fully charged, so do the following:
```bash 
pacman -Syy
```

After this finishes synching with the repositories, you're going to want to download the following package:
```bash 
pacman -S brightnessctl
```

After installing, enter this a couple times until you reach the desired brightness level:
```bash 
brightnessctl set 10%+
```


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
There are a number of ways to partition your hard drive for any Linux distro, but I prefer to use **cfdisk** to partition my disks. I also prefer a boot partition, a root partition, a swap partition, and a home partition, all in that order. Boot, Root, Swap, Home. Remember that for all future Linux Distro installations, as this applies to all distros.

 - boot partition       512M             fat32
 - root partition       25G to 50G       ext4
 - swap partition       4G to 8G
 - home partition       remainder        ext4

If you have an alternative partitioning schema, go ahead and feel free to use whatever you want. However, I will continue this section as though you chose mine:
```bash 
cfdisk
```

In cfdisk, you'll see a list of partitions, some options below such as [Bootable], [Delete], [Quit], [Write], etc. If this is not a new hard drive, go ahead and delete every partition listed form the top. If you have no partitions already, proceed to choose [New] and set the partition to be 512M. Hit the down arrow to move to the next empty space on the hard drive, then hit [New] again and type 25G or more, it really just depends on the size of your drive. Then hit [New] again and enter 4G or more, again, depending on the size of the drive. Lastly, hit [New] one more time and leave it at whatever the remaining space is. This will be the home partition, and you want to use the remaining disk space for your users, their personal files and programs, etc. Finally, choose the [Write] option, type out yes when asked to confirm changes, then press [Exit].

Confirm you did all that correctly by typing:
```bash 
lsblk
```

If everything was done correctly, you should see something like this:

```bash
sda
|-sda1     512M
|-sda2     25G
|-sda3     4G
'sda4      whatever you had left after the first 3 partitions
```

Next, lets format by doing executing the following commands:
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
genfstab -U /mnt > bash /mnt/etc/fstab
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

Lastly, let's permanently disable the pcspkr module. Use a text editor to create the following file: **/etc/modprobe.d/nobeep.conf** and then enter this text into the file:

```bash
blacklist pcspkr
```

No more obnoxious beeping.

That's everything you needed to know for both installation and post-installation, and if you have any questions, email me: nickolas@nliaquin.xyz
