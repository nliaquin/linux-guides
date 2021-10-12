# Using Wifi from the Commandline
There are 2 major components to connecting to the internet via the commandline; a dhcp client and a network manager. Today I'm going to push iwd, which is a network manager available to all distros via the mainstream package managers and the latest up and coming networking software that is replacing NetworkManager. On top of that, I'll be recommending dhcpcd and dhclient as the classing dhcp software used on all distros.

### Table of Contents
- [Required Packaged](#required-packages)
- [Confirguring iwd](#configuring-iwd)
- [Configuring dhcp](#configuring-dhcp)

### Required Packages
I shouldn't have to tell you, but this requires some thinking ahead. If you're installing Gentoo or Arch, you need to install and enable both packages during the chroot environment stage of installing the distros. Once chrooted or arch-chrooted in, you need to install iwd and dhcpcd like so (on Arch):
> pacman -Sy dhcpcd dhclient iwd

Sorry Gentoo users, I'll come back for you later.

After you install the above packages, you need to enable them via the init system:
> systemctl enable iwd

> systemctl enable dhcpcd

Don't attempt to configure these programs in chroot, nothing you do will work post-installation and you'll have to do the next steps all over again anyway. Finish your installation, exit the live environment, log in as root.

### Configuring iwd
First you need to figure out your network device. Are you on ethernet? Your network device is eth0. Are you on wifi? You network device is wlan0.

Confirm your device by starting iwd:
> iwctl

Then list your network devices:
> station list

If this comes up with nothing, exit iwd and enter the following commands:
> systemctl enable rfkill-unblock@all

> rfkill unblock wlan0

Now go back into iwd:
> iwctl

List all network interfaces with the following command:
> station list

Scan for wifi networks:
> station wlan0 scan

List wifi networks:
> station wlan0 get-networks

Connect to a wifi network:
> station wlan0 connect **network name**

If there is a password, it'll ask for it. Type it and hit enter. No confirmation will be given upon successful connection, but failed connection will give a warning.

### Configuring dhcp
All you really need to do is this:
> ip link set wlan0 up

> dhclient wlan0

That's it. Now just ping a website a couple times:
> ping -c2 google.com

If it worked, you'll successfully send icmp requests and get feedback from google.com.

If it didn't... well, you'll either have to repeat a couple of steps in this guide or email me at nickolas@nliaquin.xyz for help. I'll be glad to assist anyone in need. This all can get very complicated, but it's worth learning if you are working towards becoming a system administrator or plan on configuring and interfacing with a home server via ssh. Thanks for using my guide.
