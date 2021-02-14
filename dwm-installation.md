# Suckless dwm Installation
This will be a guide for Debian-based distros, but the steps are nearly all the same.

## Step 1 | Dependencies
For distros with aptitude package manager, you're going to want to run the following in a temrinal:

> apt install git xorg build-essential libx11-dev libxinerama-dev libxft-dev libwebkit2gtk-4.0-dev suckless-tools stterm

Alternatively, if pacman is your standard package manager, run this instead:

> pacman -Sy git xorg base-devel suckless-tools stterm

### Step 2 | Cloning dwm from Suckless.org
Before we continue, make sure you are either in your home directory or in a directory whose parent is your home directory. I personally prefer to make a directory in my home called .dwm and cd into it before cloning.

> mkdir .dwm && cd .dwm

Now we want to use git to clone from the master repo on Suckless' website with the following command:

> git clone https://git.suckless.org/dwm

I prefer their configuration with very minor tweaks. I might make a guide later on how to modify the configuration file, but if you're new to dwm, you're going to want to avoid making changes for now and study the keyboard shortcuts dwm uses by default.


### Step 4 | Configuring .xinitrc and .bash_profile
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

### Step 5 | Compiling dwm
After you cloned dwm, you should now have a folder called dwm in your current directory. Type the following command:
> ls

You should see a list of directories, and one of them will be called dwm/. Go into that directory with the following command:
> cd dwm/;ls

The last command took you into the directory, and then it ran a second command after the semicolon to list everything in the directory. You should see a file called 'config.def.h'. This file contains all of the configurations for your dwm instance. I will teach you how to modify this file in the next section, but for now, just type the following:
> sudo make clean install

Congratulations, you've just compiled vanilla dwm and are free to reboot once more. After rebooting this time, you'll log in at runlevel 1, but then notice after entering your credentials that dwm comes up. I will be creating a guide on how to patch dwm in the near future, and there may even be a guide soon on how to install dwm alongside other desktop environments so you can choose between dwm and, say, Xfce4 or KDE.
