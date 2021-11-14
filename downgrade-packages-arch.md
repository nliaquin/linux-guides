# Downgrading Packages on Arch
I just want to create a quick guide here on how to downgrade packages on Arch. Sometimes the latest version of a package really messes things up, and there is a useful utility to bring a package back to the way it was prior.


### Installing downgrade
For starters, you're going to be cloning the following repository in the location of your choosing:
> https://aur.archlinux.org/downgrade.git

You can find the AUR page at https://aur.archlinux.org/packages/downgrade/

Next, we're going to cd into the folder you just cloned:
> cd downgrade/

Next we're going to use makepkg to install dependencies and create the package:
> makepkg

And then we can finally actually install downgrade:
> sudo pacman -U sudo pacman -U downgrade-**version-of-package**-.pkg.tar.zst

This will install everything you need to continue.


### Using downgrade
If you know the name of the package you want to downgrade, just type in
> sudo downgrade **package***

Let's pretend we're downgrading firefox:
> sudo downgrade firefox

```bash
[user@distro downgrade]$ sudo downgrade firefox
Available packages (extra):

   76)  firefox    89.0.2  1  remote
-  77)  firefox    90.0    1  remote
-  78)  firefox    90.0    1  /var/cache/pacman/pkg
   79)  firefox    90.0.1  1  remote
-  80)  firefox    90.0.2  1  remote
-  81)  firefox    90.0.2  1  /var/cache/pacman/pkg
-  82)  firefox    91.0    1  remote
-  83)  firefox    91.0    1  /var/cache/pacman/pkg
   84)  firefox    91.0.1  1  remote
   85)  firefox    91.0.2  1  remote
   86)  firefox    92.0    1  remote
   87)  firefox    92.0    2  remote
-  88)  firefox    92.0.1  1  remote
+  89)  firefox    92.0.1  1  /var/cache/pacman/pkg


select a package by number: 
```

The prompt is now expecting you to pick a version to downgrade to. The **+** symbol indicates the version you have installed. The **-** symbol is a version you once had installed. Once you've picked a number, it will downgrade the package and ask if you wish to add the package to a list of packages pacman ignores when upgrading. It's fully up to you if you wish to add the package to the ignore list, but in case you want to remove that package from the list if you did ask to add it, do the following:
> vim /etc/pacman.conf

Find the part of the file where it says **IgnorePkg = firefox** and remove the line completely. Same thing goes for any other package.

And that's it! If you have any questions, feel free to email me at nickolas@nliaquin.xyz
