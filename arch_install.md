# Installing Arch Linux (Manually)
As of May 2021, Arch Linux officially comes with a guided installer. However, it's not what everyone expected... While some will be resorting to automatically installing Arch Linux from now on, many users will still install Arch manually from the ground up. Installing Arch the traditional way, much like installing older Linux distros before guided installations became the norm, is a matter of practice and having the right guide. The Arch wiki has a page dedicated to the manual install process, but many details are not fleshed out and require users to go down rabbit holes of wiki-page after wiki-page to get a full picture. In this guide, I'm not only going to show you how to install Arch, I'm going to instruct you on how to best optimize your installation, as well as how to install the boot loader and a few extra important details the Arch wiki does not cover.

### Table of Contents
- [Checking the EFI Status](#checking-the-efi-status)
- [Connecting to the internet](#connecting-to-the-internet)
- [Updating the System Clock](#updating-the-system-clock)
- [Partitioning Your Hard Drive](#partitioning-your-hard-drive)
- [Mounting the Partitions](#mounting-the-partitions)
- [Setting Pacman Mirrors](#setting-pacman-mirrors)
- [Pacstrapping root](#pacstrapping-root)
- [Generating fstab](#generating-fstab)
- [Chrooting In](#chrooting-in)
- [Setting the Timezone](#setting-the-timezone)
- [Configuring Localization](#configuring-localization)
- [Configuring Networking](#configuring-networking)
- [Setting Root Password](#setting-root-password)
- [Installing and Configuring GRUB](#installing-and-configuring-grub)
- [Post-Installation](#post-installation)

### Checking the EFI Status
All modern computers are EFI-boot, but you might be working with an older motherboard, so you'll want to make sure you know whether or not your computer is using EFI.

To check, enter:
> ls /sys/firmware/efi/efivars

One of two outcomes occur. Either you'll see a whole bunch of output, an error, or nothing at all. If you see a whole bunch of output on the screen, that means you have efi vars, and thus, efi. Had there been an error, or nothing at all, this means you have BIOS-boot. This only affects one factor of the installation, the boot loader. Don't worry about this for now and continue.

### Connecting to the internet
The Arch installation medium unfortunately will take you very far without an internet connection. This is because Arch is a minimalast distro which only contains a small numbe rof pre-installed utilities, such as standard GNU and Linux programs, shells, etc. You'll have to learn how to connect to wifi via the commandline if you don't have ethernet, but don't fret! A newer utility called iwd comes standard with the Arch installation medium. This program is far easier to use compared to NetworkManager, which is soon to be deprecated, or at least will be phased out once a number of desktop environments no longer depend on it.

For reference, iwd is spawned via the commandline as follows:
> iwctl

You'll be put into a commandline interface, and you'll now want to list your network devices:
> station list

At this point, one of two things will occur. Either you'll see a device named something like wlan0, you'll see nothing, or you'll see other devices such as enp2s0. The fact is, we need a wlan device for wifi, and if you don't see wlan0, or something that starts with wlan, you need to exit iwd by either typing exit, or pressing ctrl+c.

If you have a wifi adapter on your device, regardless if it's PCI or USB, you have a wlan device, but it's currently being blocked. To unblock it, type the following:
> rfkill unblock wifi

This was a temporary unblocking of your wlan device. We'll whitelist it permanently later in the guide. For now, type in iwctl again, and then use **station list** once more to see your network interface. If wlan0 shows up, you'll want to scan for networks like so:
> station wlan0 scan

After scanning, you can list your networks using:
> station wlan0 get-networks

If you see your wifi network, or if you already knew your network name, all you have to do is:
> station wlan0 connect **Network Name**

Auto-completion is built into iwd. Typing the first couple of letters/numbers of your wifi network and pressing tab will autofill your networks ssid. You will be prompted to enter the network passphrase, and once you successfully connect to the wifi, you'll get no confirmation. Just exit the program and move forward. You will get a warning if you entered the wrong passphrase.

After exiting iwd, enter the following to test your connection:
> ping -c3 google.com

This just pings 3 times so you don't have to cancel or exit the ping program.

### Updating the System Clock
If the system clock is innacurate, there will be complications regarding the package manager. Enter the following to ensure that your system clock will be accurate during installation:
> timedatectl set-ntp true

### Partitioning Your Hard Drive
There are a number of ways to partition your hard drive for any Linux distro, but I prefer to use **cfdisk** to partition my disks. I also prefer a boot partition, a root partition, a swap partition, and a home partition, all in that order. Boot, Root, Swap, Home. Remember that for all future Linux Distro installations, as this applies to all distros.

 - boot partition       512M             fat32
 - root partition       25G to 50G       ext4
 - swap partition       4G to 8G
 - home partition       remainder        ext4

If you have an alternative partitioning schema, go ahead and feel free to use whatever you want. However, I will continue this section as though you chose mine:
> cfdisk

In cfdisk, you'll see a list of partitions, some options below such as [Bootable], [Delete], [Quit], [Write], etc. If this is not a new hard drive, go ahead and delete every partition listed form the top. If you have no partitions already, proceed to choose [New] and set the partition to be 512M. Hit the down arrow to move to the next empty space on the hard drive, then hit [New] again and type 25G or more, it really just depends on the size of your drive. Then hit [New] again and enter 4G or more, again, depending on the size of the drive. Lastly, hit [New] one more time and leave it at whatever the remaining space is. This will be the home partition, and you want to use the remaining disk space for your users, their personal files and programs, etc. Finally, choose the [Write] option, type out yes when asked to confirm changes, then press [Exit].

Confirm you did all that correctly by typing:
> lsblk

If everything was done correctly, you should see something like this:

```bash
sda
|-sda1     512M
|-sda2     25G
|-sda3     4G
'sda4      whatever you had left after the first 3 partitions
```

Next, lets format by doing executing the following commands:
> mkfs.fat -F32 /dev/sda1

> mkfs.ext4 /dev/sda2

> mkswap /dev/sda3

> mkfs.ext4 /dev/sda4

### Mounting the Partitions
Now that we've created the partitions, we have to mount them into the right places. On Arch, /mnt is the location where you'll be setting up Arch for use.

An important thing to remember is that you want to mount your root partition first:
> mount /dev/sda2 /mnt

Before continuing, remember whether *ls /sys/firmware/efi/efivars* produced output. If yes, do the following:
> mkdir /mnt/boot && mkdir /mnt/boot/efi

> mount /dev/sda1 /mnt/boot/efi

If no, do this instead:
> mkdir /mnt/boot

> mount /dev/sda1 /mnt/boot

More on this later...

Then we'll activate the swap partition:
> swapon /dev/sda3

Lastly, we mount home:
> mkdir /mnt/home

> mount /dev/sda4 /mnt/home

### Setting Pacman Mirrors
This is an optional step that can be beneficial in two ways: the first being faster download speeds when installing or updating packages. The second being that if you're on campus wifi, work wifi, or any network that is not your own, meaning you did not configure it, mirrorlists are a potential step to getting around blacklisted sites on the network. If you find yourself unable to download any packages while on a given connection, and you're not using a mirrorlist, I heavily encourage doing this.

First, synchronize the pacman databases:
> pacman -Syy

Once this is done, install git:
> pacman -S git

Use git to download my secure mirror list:
> git clone https://github.com/nliaquin/arch-mirrors

One problem a lot of people have is a message stating *GLIBC_2.33 not found (required by git)**
> pacman -S glibc

That resolves that issue, now rerun my git clone command and it will work.

A quick side-note: If you can't use pacman because you do not have a mirrorlist set up beforehand, you can easily download my mirrorlist from any other computer, put it on a flash drive, and mount that flash drive while installing Arch. Then proceed to do the following.

Simply move the mirrorlist to the following destination:
> mv arch-mirrors/mirrorlist > /etc/pacman.d/

I keep this list updated once a month as a convenience. Feel free to use it yourself from now on when it's time to update your mirrors.

### Pacstrapping root
Usually during this step, people just install the standard packages, but I think this is actually a good time to install some other essentials like iwd, which does not come with Arch post-installation, dhcpcd, which will help with resolving hosts when you boot in post-installation, and other great utilities:

> pacstrap /mnt base base-devel linux linux-firmware iwd efibootmgr vim git dhcpcd dhclient bash man-db man-pages texinfo openssh sudo

This is a ton of stuff to help you get going, but it's not everything. This does not cover graphical environments, video, audio, etc. I have more guides on that at https://github.com/nliaquin/linux-guides

### Generating fstab
In order to boot properly into the system each time we start up, you need to create a table with all of the partition mapping information in /etc/fstab:
> genfstab -U /mnt >> /mnt/etc/fstab

### Chrooting In
This next step will allow you to modify your base Arch system before you even finish installing it. Basically, you're about to start modifying your distro as if it's already installed before you even run it. Chrooting into Linux has a number of different use-cases, such as system recovery. From this point forward, everything we do directly affects Arch post-installation:
> arch-chroot /mnt

### Setting the Timezone
Let's say you live in New York, you would run the following:
> ln -sf /usr/share/zoneinfo/America/New_York /etc/localtime

If you lived in Detroit, you would have written America/Detroit instead.

Adjust the time to fit that region change:
> hwclock --systohc

### Configuring Localization
Use nano or vim to modify /etc/locale.gen and uncomment out the line down below where it just says the following:
> #en_US.UTF-8 UTF-8

Now initialize the locale file:
> locale-gen

Set the encoding language to, for instance, UTF-8 for US computers:
> echo "LANG=en_US.UTF-8" > /etc/locale.conf

This just ensures that when programs look for your locale, they can default to it instead of C. Most programs will default to C if this isn't done, which is fine, but you'll just keep getting errors every time you run programs looking for the locale.gen value while still doing what you've asked.

### Configuring Networking
Giving your machine a host identity is crucial, as Linux requires a hostname in order for certain networking functions to work properly. Let's say we're naming our machine arch-laptop:
> echo arch-laptop > /etc/hostname

Next, use vim or nano to modify your system's hosts file:
> vim /etc/hosts

Once in editing mode, write out the following, given you went with arch-laptop as your host name:

```bash
127.0.0.1   localhost
::1         localhost
127.0.1.1   arch-laptop.localdomain arch-laptop
```

Now you'll want to enable iwd and dhcpcd at startup so that networking just works on boot:
> systemctl enable iwd

> systemctl enable dhcpcd

Lastly, if you had to use rfkill in order to unblock wlan0, run the following command:
> systemctl enable rfkill-unblock@all

### Setting Root Password
Because we need to log into Arch as root post-installation on the first go, you need to set a root password before you forget like so:
> passwd

You'll get a message asking you to set the password. Set it to something good, but don't plan on using root as your main user. Many programs will not allow this in the Linux community.

### Installing and Configuring GRUB
Grub is the bootloader that gives you the ability to actually start Linux after booting. You'll need to first download grub like so:
> pacman -S grub

From here, we can do this two different ways: if *ls /sys/firmware/efi/efivards* produced results, do the following:
> grub-install --target=x86_64-efi --efi-directory=/boot/efi

If not, do this instead:
> grub-install --target=i386-pc

Lastly, export your grub configuration:
> grub-mkconfig -o /boot/grub/grub.cfg

Once that's all finished, exit chroot:
> exit

Lastly, unmount root:
> umount -R /mnt

Now you may reboot:
> reboot

### Post-Installation
A problem you might run into is with internet after finishing the installation.

What to do if dhcp doesn't work on startup:
> ip link set wlan0 up

> dhclient wlan0
