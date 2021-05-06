# Installing Arch Linux (Quickly)
This isn't so much an in-depth guide on installing Arch, that you can get on the Arch Wiki. No, this is the quick and most efficient way to manually install Arch.
I've added some steps the official guide does not include, but is vital to ensuring the best outcomes post-installation. I still advise everyone to check out the official guide some time.

If you feel any of the commands are too long, you can always clone this guide using git and use vim to copy and paste commands directly from here.

### Check EFI Status
Whether or not your motherboard uses EFI, you're going to want to check with the following command:
> ls /sys/firmware/efi/efivars

If this comes up with nothing, you're on an older system. Honestly, the installtion is the same regardless. I've always installed Arch the same way on all of my devices, EFI or not.
If a bunch of results pop up, you are in an EFI environment, which means you'll have the ability to put your system into hibernation provided you make a swap partition. We're gonna do that.


### Connecting to the internet
It doesn't matter whether your network card is blocked or not, it's easier just to run the following command than to try connecting to the internet and failing:
> rfkill unblock wifi

What we've just done is enabled wifi if it was blocked on startup.

Next we need to use a program called iwd in order to connect to wifi from the commandline:
> iwctl

You should be in a CLI environment now where your $ will be **[iwd]#** . Next type this:
> station list devices

What we're doing is looking at your available networking devices. You should see *wlan0* at this time. Next type:
> station wlan0 scan

Nothing will happen... yet. Type in:
> station wlan0 get-networks

All this does is shows the nearby wifi networks. Once you've identified your network, let's say it's **HomeNetwork**, type in the following:
> station wlan0 connect HomeNetwork

It'll ask you for the network password. After typing it in, nothing will indicate you successfully got on the network. This is okay, just type exit or hold **ctrl + c**

Ping google or a website of your choice real quick and verify you have a network connection:
> ping -c3 google.com

This just pings 3 times so you don't have to cancel or exit the ping program.


### Updating the system clock
Just type in:
> timedatectl set-ntp true

That's it for now. More on the clock and timezone later...


### Partition the disk
I prefer to use **cfdisk** to partition my disk. I also prefer a boot partition, a swap partition, a root partition, and a home partition.

 - boot partition       512M        fat32
 - swap partition       8G          doesn't matter
 - root partition       25G         ext4
 - home partition       remainder of the disk, also ext4

Don't get hung up on my particular schema, just know that if you plan on using Linux for the usual home desktop environment; gaming, work, production, etc; this schematic above works just fine.

I'm not going to show you how to partition drives here, but I will show you how to format them and mount them. Partitioning is a guide on its own that I'll write when I have time later.

After writing your partitions to the disk and quitting cfdisk, run the following to check your partitions on the installation disk are properly written:
> lsblk

Provided you've written to a hard drive with nothing on it, or you deleted prior partitions, you should see something like:
 sda
  sda1     512M
  sda2     8G
  sda3     25G
  sda4     whatever you had left after the first 3 partitions

Next, lets format by doing executing the following commands:
> mkfs.fat -F32 /dev/sda1

> mkswap /dev/sda2

> mkfs.ext4 /dev/sda3

> mkfs.ext4 /dev/sda4


### Mounting the partitions
An important thing to remember is that you want to mount your root partition first:
> mount /dev/sda3 /mnt

Then we'll activate the swap partition:
> swapon /dev/sda2

Let's make a couple of needed directories real quick:
> mkdir /mnt/boot && mkdir /mnt/boot/efi && mkdir /mnt/home

Now we'll mount boot:
> mount /dev/sda1 /mnt/boot/efi

Lastly, we mount home:
> mount /dev/sda4 /mnt/home

We're done mounting.


### Add mirrors to /etc/pacman.d/mirrorlist
This is an optional step that can speed up your connection to the Arch repositories give you live in the United States.

Sychronize pacman database
> pacman -Syy

Once this is done, install git:
> pacman -S git

Use git to download my secure mirror list:
> git clone https://github.com/nliaquin/arch-mirrors

One problem a lot of people have is a message stating *GLIBC_2.33 not found (required by git)**
> pacman -S glibc

That resolves that issue, now rerun my git clone command and it will work.

Now all you have to do is concatenate my list to the pacman mirrorlist file:
> cat arch-mirrors/mirrorlist > /etc/pacman.d/mirrorlist

I keep this list updated once a month as a convenience. Feel free to use it yourself from now on when it's time to update the mirrors.


### Pacstrapping root
Usually during this step, people just install the standard packages, but I think this is actually a good time to install some other essentials like iwd, which does not come with arch post-installation, and dhcpcd, which will help with resolving hosts when you boot in post-installation:

> pacstrap /mnt base base-devel linux linux-firmware iwd efibootmgr vim git dhcpcd dhclient bash man-db man-pages texinfo openssh wget curl sudo

This is a ton of stuff to help you get going, but it's not everything. This does not cover graphical environments, video, audio, etc. I have more guides on that at https://github.com/nliaquin/linux-guides


### Generating fstab
In order to boot properly into the system each time we start up, you need to create a table with all of the partition mounting information in /etc/fstab:
> genfstab -U /mnt >> /mnt/etc/fstab


### Chrooting in
We will now chroot into the Arch instance we've been building on /mnt and performing some required network changes:
> arch-chroot /mnt

We're in.


### Set the time-zone, clock, localization, and so on
Let's say you live in New York, you would run the following:
> ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime

If you lived in Detroit, you would have written America/Detroit instead.

Adjust the time to fit that region change:
> hwclock --systohc

Set the encoding, for instance, UTF-8 for American computers:
> echo "LANG=en_US.UTF-8" > /etc/locale.conf

Generate locale file:
> locale-gen

Giving your machine a host identity is crucial, because it's how your PC identifies your localhosts' name. Let's say we're naming our machine arch-laptop:
> echo arch-laptop > /etc/hostname

The next step defines your host's identity to your PC's local network configuration. You're going to use a text editor such as vi, vim, or nano like so:
> vim /etc/hosts

Once in editing mode, write out the following, given you went with arch-laptop as your host name:

`127.0.0.1   localhost`
`::1         localhost`
`127.0.1.1   arch-laptop.localdomain arch-laptop`

If you don't do this, the networking on your instance of arch doesn't work properly.

Now you'll want to enable iwd and dhcpcd so that networking just works on next boot:
> systemctl enable iwd

> systemctl enable dhcpcd


### Set the root password
Because we need to log into Arch as root post-installation on the first go, you need to set a root password before you forget like so:
> passwd

You'll get a message asking you to set the password. Set it to something good, but don't plan on using root as your main user. Many programs will not allow this in the Linux community.


### Installing and Configuring GRUB
Grub is the bootloader that gives you the ability to actually start Linux after booting. You'll need to first download grub like so:
> pacman -S grub

Once it finished downloading, install grub as follows:
> grub-install --target=x86_64-efi --efi-directory=/boot/efi

Now configure grub:
> grub-mkconfig -o /boot/grub/grub.cfg

Once that's all finished, exit chroot:
> exit

Lastly, unmount root:
> umount -R /mnt

Now you may reboot:
> reboot


### Post-Installation
A problem you might run into is with internet after finishing the installation.

What to do if iwd doesn't pick up eth0 or wlan0:
> systemctl enable rfkill-unblock@all

What to do if dhcp doesn't work on startup:
> ip link set wlan0 up

> dhclient wlan0

If you're using ethernet, replace wlan0 with eth0.

### A note on EFI
You may be wondering why I say to follow this guide as is, even if you do not have and EFI motherboard. Well, it still just works. I have three devices without EFI, and this is exactly how I installed Arch and Gentoo on them.
Why does this work? This configuration is functional for all systems, regardless. Boot partitioning is arbitrary on non-efi systems. EFI has a particular setup for booting, which is why you MUST follow this for EFI systems.

If you had any trouble, feel free to contact me via email at nickolas@nliaquin.xyz and feel free to check out my other guides and repos at https://github.com/nliaquin
