# Suckless dwm Installation
For this guide, we will be not be depending on the package manager beyond the first step. I will show you how to clone dwm, dmenu, and a couple patches, and then I will show you how to configure your initialization files. Finally, I will demonstrate how you compile dwm and its patches. There is a guide currently in the works on optimizing and coding your own changes into dwm.

## Step 1 | Dependencies
To the newer Linux users, no matter what distro you're using, you will need git.

For distros that use the aptitude package manager, the following packages are needed:

* build-essential - necessary to compile software
* xorg - a display server
* libx11-dev - provides control over xorg
* libxft-dev - a library for xorg font rendering
* libxinerama-dev - provides an xorg window System client interface
* libwebkit2gtk-4.0-dev - helps display web content to xorg

There descriptions are simplifications, but they give you an idea of what we're working with. You can run the following command to install all of the above:

> apt install build-essential xorg libx11-dev libxinerama-dev libxft-dev libwebkit2gtk-4.0-dev

If you're on a system that uses the pacman package manager, you'll just need the following:

> pacman -Sy xorg xorg-xinit base-devel

Just a note, base-devel is just the arch-equivalent to build-essential.


### Step 2 | Cloning dwm from Suckless.org
We can clone into a number of locations, but it's probably best to make a hidden directory in your home folder, like .suckless, and clone everything into there.

First we'll clone the base, which is dwm itself:

> git clone https://git.suckless.org/dwm

Now we'll clone dmenu, which is basically the core of suckless utilities:

> git clone https://git.suckless.org/dmenu

You may want to clone suckless' terminal, stterm (simple terminal), but I typically use my own terminal. Run this for stterm:

> git clone https://git.suckless.org/st

And finally, I prefer dwmblocks for configuring my status bar, so go ahead and run the following for an easy to use, easy to configure status bar:

> git clone https://github.com/torrinfail/dwmblocks


### Step 3 | Compiling dwm
This next part is going to be tedious, but since we aren't configuring right now, only compiling, this'll be fairly straightforward. Move into the dwm directory.

The next command is going to use a makefile that is in the current directory. Make files are used to execute a set of protocols and commands that a developer sets for their c-type programs in order for the program to work as intended. You can learn more here: https://opensource.com/article/18/8/what-how-makefile

Understand that in the case of these programs that were designed for dwm, as well as dwm itself, the file being compiled if the config.def.h file. A lot of people have misconception that they should modify config.h, but when making changes to dwm, the best practice is to remove config.h, and modify config.def.h, and *then* compile. To compile dwm and its patches, run the following:

> sudo make clean install

What you've just done is compile a config.h file and extracted the executables into their respective /usr/ directories in order for dwm to work like any other Linux c program. So, just one last heads up, when you read my guide on configuring dwm and its patches, make sure to follow the directions and delete the config.h file before recompiling config.def.h for safety. We want to make sure your changes work, and this is the only way to do so.

Next we need to cd back and into the dmenu directory, and then run the previous make clean install command again. You'll once again cd back, and then into the st directory, then run the command again. Do it once more to the dwmblocks directory, and you've successfully compiled everything you'll need to get dwm and its patches working correctly. If this is confusing, feel free to contact me: nickolas@nliaquin.xyz

### Before we continue...
You're now going to have to make a choice before moving forward:
**Do you want to have dwm and the option of using other desktop environments** or **do you just want dwm**?

Both choices have relatively the same amount of steps involved in setting up, but it's the difference between exclusively running dwm with no display manager, which is usually my prefered method as the startup is instantaneous, and being able to use a different desktop environment if you so choose.

If you want to be able to run other desktop environments, read **Step 4b | Configuring dwm for lightdm**.
If you want to run dwm exclusively with no display manager or alternative desktop environmentsm, read **Step 4a | Configuring dwm for Xorg**.

### Step 4a | Configuring dwm for Xorg
While in your home directory, and if you're not, use this command to get there (for the beginners)

> cd ~

use a text editor to either edit or create .xinitrc. I prefer vim as my text editor.

> vim .xinitrc

If using vim, press the 'i' key, and type the following:

> exec dwm

Press escape and type the following:

> :wq

The semicolon means you're running a command, the w means write to the file, the q means quit the program.

Now you only need to configure your .bash_profile

> vim .bash_profile

Then type the following:

> startx

Same as before, press escape and type the following to save and quit:
> :wq

### Step 4b | Configuring dwm for lightdm
Run the following command to install lightdm:

> sudo apt install lightdm

We don't really have to navigate anywhere to do this next part, you can simply run the following command:

> sudo vim /usr/share/xsessions/dwm.desktop

What we're doing is creating a file recognized by lightdm as a *desktop entry* for dwm. Installing Xfce4 or Gnome will always create desktop entries for you, but dwm requires us to set it up ourselves. Press *i* or the *insert* key to enter write mode in this file, then enter the following information:

[Desktop Entry]
Encoding=UTF-8
Name=dwm
Comment=Suckless dynamic window manager
Exec=dwm
Icon=dwm
Type=XSession

Press the escape key and type in the following to save and quit:

> :wq!

Now all that's left is to reboot, which is best done by entering 

> sudo reboot

### Step 5 | Aftermath
After rebooting, you may notice your monitors are not ordered correctly, given you have multiple monitors. Please check the guide titled *xrandr-reorder-monitors* to learn how to resolve this, and to also learn some tips on how to make good use of xrandr.

If you notice only a black screen when rebooting, this is likely because you already had proprietary drivers or nvidia drivers installed to manage your display. This guide supports xorg and xsessions, but I unfortunately do not have any advice for nvidia gpu owners at this time. I am also having issues with nvidia on Debian-based distros while running dwm, and quite frankly, you're better off using PopOS or Manjaro for that sort of thing.

One more thing: if you find that wlan0 is unavailable at startup, use the following command after rebooting and logging into root:
> systemctl enable rfkill-unblock@all

This tells systemd, the init system, to always unblock networking.

If you have any questions or would like some help, email me at nickolas@nliaquin.xyz
