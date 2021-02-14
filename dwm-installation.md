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


### Step 3 | Compiling dwm
Now that we actually have dwm, we need to compile it. You will need to go into the directory you cloned using git with the following:

> cd dwm/

The next command is going to use a makefile that is in the current directory. Make files are used to execute a set of protocols and commands that a developer sets for their c-type programs in order for the program to work as intended. You can learn more here: https://opensource.com/article/18/8/what-how-makefile

For now, just run the following command in the terminal:

> make clean install

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

`[Desktop Entry]
Encoding=UTF-8
Name=dwm
Comment=Suckless dynamic window manager
Exec=dwm
Icon=dwm
Type=XSession`

Press the escape key and type in the following to save and quit:

> :wq!

Now all that's left is to reboot, which is best done by entering 

> sudo reboot

### Step 5 | Aftermath
After rebooting, you may notice your monitors are not ordered correctly, given you have multiple monitors. Please check the guide titled *xrandr-reorder-monitors* to learn how to resolve this, and to also learn some tips on how to make good use of xrandr.

If you notice only a black screen when rebooting, this is likely because you already had proprietary drivers or nvidia drivers installed to manage your display. This guide supports xorg and xsessions, but I unfortunately do not have any advice for nvidia gpu owners at this time. I am also having issues with nvidia on Debian-based distros while running dwm, and quite frankly, you're better off using PopOS or Manjaro for that sort of thing.

If you have any questions or would like some help, email me at nickolas@nliaquin.xyz
