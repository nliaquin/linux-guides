# Suckless dwm Installation & Configuration for Beginners
*Propritaries:* Debian-Based Distros
*Dependencies:* Git, Xorg, and several devtools that will be listed below
*Difficulty:* Moderately Difficult

## Preface
I made this as my first guide on here and removed my dwm.sh script in my apt-scripts repo, as well as my dwm repo, for a few reasons.

- I removed my dwm.sh script because it doesn't always work correctly depending on the distro. Everything I wrote in it is *correct* for the majority of Debian-based distros, the problem is that, on a distro like Ubuntu, I did not realize you have to reboot before removing gdm3, set up .xinitrc and .bash_profile, reboot again, and then you can start to install and configure dwm. On the three distros I have on my machines, typically you can just run that script once, reboot one time, and everything works. I discovered Pop OS has this problem, too. I'm typically a Debian and Kali user, so I don't always know what's going to happen on specific distros.

- I now have multiple dwm configs for specific machines. On my desktop, I limit my tags to three per screen with three monitors total. On my laptops, I like to have six to nine tags. On one of my machines, I prefer the Super key to be Ctrl, and on my other machines it's the Windows key. This is why I got rid of my dwm repo, there's just no point. I may as well just git clone from suckless and customize from scratch.

- I wanted to create a guide that just works on any Debian-based distro. You should pull this guide up on a phone or other device, because you're going to be rebooting a few times to make sure this goes right.

## Installing Suckless dwm
### Step 1 | Dependencies
For this to work on Debian-based distros, you're going to need Git, Xorg, Build-Essential, and a few developer packages that if you want to know what they are or what they're for exactly, I'll advise you to check out my guide on dpkg and apt, available soon in this repo. There will be a section on how to list information of a given package, but for now, run this command in a terminal to install them:

> sudo apt install git xorg build-essential libx11-dev libxinerama-dev libxft-dev libwebkit2gtk-4.0-dev suckless-tools stterm

Now just reboot and continue to step two. If you're newer to Linux, the best commands to reboot are either one of the following:
> sudo reboot
> systemctl reboot
The first one will more often than not require a password, and the second more often than not won't require a password.

### Step 2 | Uninstall gdm3/lightdm
Now you need to uninstall whichever display manager you have, and typically you'll have gdm3 if you have Ubuntu or Pop OS, but if you have a distro like Mint or Kali, you'll likely have lightdm. Either way, you can try both of these commands in the terminal, as they won't do any harm.

> sudo apt remove gdm3
> sudo apt remove lightdm

Now reboot once more.

### Step 3 | Cloning dwm from Suckless.org
There are several ways to get dwm on your system, but my favorite way is to clone it from the master git repo on the Suckless website. If you're new to Linux, we just installed a program called git in step one. To clone dwm from Suckless' git server, enter the following command in the terminal:
> git clone https://git.suckless.org/dwm

Thats it, you now have it on your system. All that's left is to compile it and reboot once more in step four.

### Step 4 | Compiling dwm
After you cloned dwm, you should now have a folder called dwm in your current directory. Type the following command:
> ls

You should see a list of directories, and one of them will be called dwm/. Go into that directory with the following command:
> cd dwm/;ls

The last command took you into the directory, and then it ran a second command after the semicolon to list everything in the directory. You should see a file called 'config.def.h'. This file contains all of the configurations for your dwm instance. I will teach you how to modify this file in the next section, but for now, just type the following:
> sudo make clean install

Congratulations, you've just compiled vanilla dwm and are free to reboot once more. After rebooting this time, you'll log in and notice dwm is running.

## Configuring dwm
# Still working on this part, come back soon...
