# Configuring Xorg for suckless dwm
Please be sure to complete this guide, the **dmenu.md guide**, and the **dwm.md** guide before moving onto any other guide in **suckless-software**.

### Table of Contents
 - [Preliminaries](#preliminaries)
 - [Home Files Configuration](#home-files-configuration)
 - [Startup sh file](#startup-sh-file)

### Preliminaries
You are going to need to install the following software if you haven't already through my other guides:
 - xorg

Run the following if you are on a Debian-based distro:
```bash
sudo apt install xorg libx11-dev libxinerama-dev libxft-dev libwebkit2gtk-4.0-dev 
```

Run the following if you are on an Arch-based distro:
```bash
sudo pacman -S xorg xorg-xinit 
```

### Home Files Configuration
If all you have thus far is xorg, dwm, and dmenu, now is the time to *cd* into *$HOME* ```cd ~``` and use your preferred text editor to create a file called **.xinitrc** and enter the following inside:
```bash
exec dwm
```

Save and exit this file, then edit **.bash_profile** with your preferred text editor and add the following to the very bottom of the file:
```bash
startx
```

### Startup sh file
If you have patched, or plan on patching, *dwmblocks* into your suckless setup, you'll want to change **.xinitrc** to the following:
```bash
sh .startup.sh
exec dwm
```
Make sure that **exec dwm** is always the last line in **.xinitrc**, otherwise this file will break your startup.

Now create a file called **.startup.sh** in your home directory if you haven't already to add the following:
```bash
dwmblocks &
```

The **startup.sh** file is where you'll add all sorts of startup commands. If you use *feh* to change your background, your **.startup.sh** can look like this:
```bash
feh *location of image*
dwmblocks &
```
It's best to keep the **dwmblocks &** line at the bottom of this file.

Other commands can be added to this file, like if you have two monitors that always are out of order when you startup, you can use xrandr commands to reorder them before you start up dwm.

I hope this guide was helpful, and I'm sorry if you were looking for a section of configuring dwm with a display manager, I no longer support that kind of setup. I have found combining dwm with display managers to be pretty buggy lately. If you have any questions, reach me via nickolasiaquinta@gmail.com
