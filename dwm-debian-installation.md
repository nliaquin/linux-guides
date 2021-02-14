# Suckless dwm & dwm blocks on Debian-based distros
*Propritaries:* Debian-Based Distros
*Dependencies:* Git, Xorg, and several devtools that will be listed below
*Difficulty:* Moderately Difficult

## Installing Suckless dwm
### Step 1 | Dependencies
First you'll need some dependencies. I advise you to look up these packages and at least understand what they are in general. Use this command in the terminal:

> sudo apt install git xorg build-essential libx11-dev libxinerama-dev libxft-dev libwebkit2gtk-4.0-dev suckless-tools stterm

Now just reboot and continue to step two. If you're newer to Linux, the best commands to reboot are either one of the following:
> sudo reboot

### Step 2 | Uninstall gdm3/lightdm
Now you need to uninstall whichever display manager you have, and typically you'll have gdm3 if you have Ubuntu or Pop OS, but if you have a distro like Mint or Kali, you'll likely have lightdm. Either way, you can try both of these commands in the terminal, as they won't do any harm.

> sudo apt remove gdm3
> sudo apt remove lightdm

Now reboot once more.

### Step 3 | Cloning dwm from Suckless.org
There are several ways to get dwm on your system, but my favorite way is to clone it from the master git repo on the Suckless website. If you're new to Linux, we just installed a program called git in step one. To clone dwm from Suckless' git server, enter the following command in the terminal:
> git clone https://git.suckless.org/dwm

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
