# Downgrading Packages
### How to revert a program to a previous version

If you've ever upgraded a package through pacman and found the latest version not to be to your liking, there is a simple solution to revert the package to one of several previous versions that doesn't require downloading an older version manually from a website. This solution entails acquiring an AUR repo called **downgrade** and installing the arch keyring.


### Installing Downgrade
First we'll need to clone the following git repo:
```bash
git clone https://aur.archlinux.org/downgrade.git
```

Go into the downgrade directory after:
```bash
cd downgrade
```

Then build the package and its dependencies:
```bash
makepkg -s
```

And once this has finished, let's install downgrade:
```bash
sudo pacman -U *.zst
```
If you're wondering why I use wildcard followed by *.zst*, it's because when you build any package from the AUR (Arch User Repository), the .zst file is always the one you use to install locally with *pacman -U*. So pretty much every time you manually build and install from AUR, keep this in mind.

Downgrade is installed, but you will need to verify package signatures when you use it, and a component that needs to be installed is the Arch Keyring:
```bash
sudo pacman -S archlinux-keyring
```


### Downgrading packages
A few things to note before downgrading anything:
 - Always run downgrade as root
 - Packages that are downgraded manually should be managed manually. If you downgrade a package, downgrade asks if you want to add it to an ignore file so when you use *pacman -Syu*, it no longer updates. More on this later.
 - Downgrading is easily reversable, do not panic if something goes wrong. Packages can be reinstalled easily.
 - Lastly, pay attention if a package requires a major security update. There is a reason why big packages are regularly maintained, so keep in mind that by keeping a package downgraded for long periods of time, you are possibly missing out on important updates.

With all that said, let's run by a scenario and downgrade a package. You find that the latest version of **ssh** has a bug and isn't working properly, and we're going to use **downgrade** to revert to an older version:
```bash
sudo downgrade ssh
```
The get the following output:
```bash
[user@distro]$ sudo downgrade ssh
Unable to downgrade ssh
```
Why is this? It's the final note I want to make on downgrading packages. Not all packages are refered to by their primary call command. The default **ssh** program on Linux is *openssh*. Always verify package names.

Let's actually downgrade our package this time:
```bash
sudo downgrade openss
```
The following output is given:
```bash
[user@distro]$ sudo downgrade openssh
Available packages (core):

    1)  openssh    8.0p1  1  remote
    2)  openssh    8.0p1  2  remote
    3)  openssh    8.0p1  3  remote
    4)  openssh    8.0p1  3  /var/cache/pacman/pkg
    5)  openssh    8.1p1  1  remote
    6)  openssh    8.1p1  2  remote
    7)  openssh    8.1p1  4  remote
    8)  openssh    8.2p1  1  remote
    9)  openssh    8.2p1  1  /var/cache/pacman/pkg
   10)  openssh    8.2p1  3  remote
   11)  openssh    8.3p1  1  remote
   12)  openssh    8.3p1  2  remote
   13)  openssh    8.3p1  3  remote
   14)  openssh    8.3p1  4  remote
   15)  openssh    8.3p1  5  remote
   16)  openssh    8.4p1  1  remote
   17)  openssh    8.4p1  2  remote
   18)  openssh    8.5p1  1  remote
   19)  openssh    8.6p1  1  remote
   20)  openssh    8.7p1  1  remote
   21)  openssh    8.7p1  2  remote
+  22)  openssh    8.8p1  1  remote
+  23)  openssh    8.8p1  1  /var/cache/pacman/pkg

select a package by number: 
```
You are now expected to enter the number corresponding to the desired previous version you would like to install. The version your currently have has a **+** next to it. If you had any previous versions installed, a **-** will be marked next to it.

Continuing our scenario, let's assume that version 8.4p1 is what I want, so type **17** and hit enter.
```bash
select a package by number: 17
:: Retrieving packages...
 openssh-8.4p1-1-x86_64                                                                906.3 KiB   764 KiB/s 00:01 [####################################################################] 100%
loading packages...
warning: downgrading package openssh (8.8p1-1 => 8.4p1-1)
resolving dependencies...
looking for conflicting packages...

Packages (1) openssh-8.4p1-1

Total Installed Size:   5.34 MiB
Net Upgrade Size:      -0.56 MiB

:: Proceed with installation? [Y/n] 
```
At this point, obviously hit enter to continue with the installation.
```bash
:: Proceed with installation? [Y/n] 
(1/1) checking keys in keyring                                                                                     [####################################################################] 100%
(1/1) checking package integrity                                                                                   [####################################################################] 100%
(1/1) loading package files                                                                                        [####################################################################] 100%
(1/1) checking for file conflicts                                                                                  [####################################################################] 100%
(1/1) checking available disk space                                                                                [####################################################################] 100%
:: Processing package changes...
(1/1) downgrading openssh                                                                                          [####################################################################] 100%
:: Running post-transaction hooks...
(1/3) Reloading system manager configuration...
(2/3) Creating temporary files...
(3/3) Arming ConditionNeedsUpdate...
add openssh to IgnorePkg? [y/N] 
```
Now, let's talk about this part. We're being asked whether we should add this package to a list that will tell *pacman* whether updating the system and its packages should also update this one. If you want to maintain the version your downgrading to, but plan on updating your system regularly, enter **y** and hit enter. Otherwise, if this is just a temporary downgrade, just hit enter to default to *No*. Remember, Arch is bleeding-edge when it comes to package updates, so it's not so dangerous to keep using a slightly older version of the package. Like I said above, just understand that the longer a package goes unupdated, the more there is a potential risk you will miss a security update. Always be sure to check on the website where the target package is maintained once every couple of months just to see if there is a critical update you're missing.

Let's assume you said *yes* to *add openssh to IgnorePkg?*. What did this do exactly? Well, there is a file in **/etc/** called **pacman.conf**, and in that file is a section called *#IgnorePkg =*. If you were to do the following:
```bash
cat /etc/pacman.conf
```
You would see roughly the following:
```bash
#
# /etc/pacman.conf
#
# See the pacman.conf(5) manpage for option and repository directives

#
# GENERAL OPTIONS
#
[options]
# The following paths are commented out with their default values listed.
# If you wish to use different paths, uncomment and update the paths.
#RootDir     = /
#DBPath      = /var/lib/pacman/
#CacheDir    = /var/cache/pacman/pkg/
#LogFile     = /var/log/pacman.log
#GPGDir      = /etc/pacman.d/gnupg/
#HookDir     = /etc/pacman.d/hooks/
HoldPkg     = pacman glibc
#XferCommand = /usr/bin/curl -L -C - -f -o %o %u
#XferCommand = /usr/bin/wget --passive-ftp -c -O %o %u
#CleanMethod = KeepInstalled
Architecture = auto

# Pacman won't upgrade packages listed in IgnorePkg and members of IgnoreGroup
#IgnorePkg   =
IgnorePkg = openssh
#IgnoreGroup =

```
Near the very bottom, you see openssh was added in after *IgnorePkg = *, and you can edit this to add more packages if you want. To add more packages, only seperate the package names with spaces, not commas. Also, once again, ensure you types in the right package name.

That's it for this guide, it definitely contains some insight not usually found in other guides, and if you have any questions just email me via nickolasiaquinta@gmail.com.
