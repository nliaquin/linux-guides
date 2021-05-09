# Install a Graphical Environment on Arch

If you read my last guide on how to install Arch Linux, you may have noticed I didn't cover what to do post-installation. There are various steps to take now, but I'm going to focus on getting the graphical environment configured and running first. Before we begin, quickly check out my networking-from-scratch.md guide, because chances are you don't have internet after booting into your new Arch installation and you need to set up dhcp.

### Xorg
You need xorg for starters, and you optionally need to figure out which component you are going to render from on your device. By default, the component that takes on the work of rendering is going to be the CPU, but if you have a graphics card, you'll need to specifiy which package you want to install alongside xorg.

First, install xorg. You can install all components of xorg with the following command:
> sudo pacman -S xorg-server

That gives you xorg, xorg-xinit, etc. You need those to get the ball rolling.

Now you can either check your rendering devices with the following command, or skip down to the next command:
> lspci -v | grep -A1 -e VGA -e 3D

Here is where you need to determine which of the following packages you're going to install.

For default (No 3D accelleration, most chips are supported):
> sudo pacman -S xf86-video-vesa

Alternatively, you can try mesa (3D accelleration, supports both AMD and ATI, but less chips overall are supported):
> sudo pacman -S mesa lib32-mesa

If one doesn't work, try the other. Remember, the nice thing about Linux is that there's always a fallback into the terminal/shell where you can fix everything. Also, you can never really go wrong with mesa and lib32-mesa, because regardless if you're using AMD or ATI, xorg will sort it out.

For AMD GPUs (Open Source):
sudo pacman -S xf86-video-amdgpu mesa lib32-mesa

For ATI GPUs (Open Source):
> sudo pacman -S xf86-video-ati mesa lib32-mesa

For AMD/ATI Proprietary Drivers:
> sudo pacman -S xf86-video-amdgpu amdgpu-pro-libgl lib32-amdgpu-pro-libgl

For Intel (Open Source):
> sudo pacman -S xf86-video-intel mesa lib32-mesa

For Nvidia (Open Source):
> sudo pacman -S xf86-video-nouveau mesa lib32-mesa

For Nvidia (Proprietary, Latest):
> sudo pacman -S nvidia nvidia-utils lib32-nvidia-utils

For Nvidia (Proprietary, Specific):
> sudo pacman -S nvidia-390xx nvidia-390xx-utils lib32-nvidia-390xx-utils

Just a heads up for that last one, you can specify which Nvidia driver you need from the last generation by replacing the **xx** with the version number. You may have to do this for 700-900 series GTX cards, like I have.

I'll make a guide later on further elaboratin Nvidia drivers, but for now, all I will say is that Nvidia can be a pain. I'm speaking from experience...


Now that you figured out which drivers you need, you're mostly good to move on, but you might need to enable xorg-xinit:
> systemctl enable xorg-xinit

### Display Manager
It's ultimately your choice what display manager you choose, but understand that each one has it's own configurations and settings. I am most familiar with lightdm, but you also have gdm3 and others. Take the time to visit the wikis for either of these display managers, get to know how they are configured, what files are used to enforce rules and settings, etc. Lightdm can be installed like so:
> sudo pacman -S lightdm lightdm-gtk-greeter lightdm-gtk-greeter-settings

And then enable the two:
> systemctl enable lightdm

> systemctl enable lightdm-gtk-greeter

That will allow your distro to start these things without the user having to, meaning systemd starts them after the initial boot process.

### Desktop Environment
Lastly, you need a desktop environment. Display Managers like lightdm enable you to use desktop environents such as Xfce4, Gnome40, MATE, etc.

To install, say Xfce4, do the following:
> sudo pacman -S xfce4 xfce4-settings xfce4-goodies

That really takes care of everything, and the xfce4 package now comes with desktop entries for lightdm and gdm3 by default these days.

I will return to this guide to talk about getting non-standard desktop environments into lightdm later. I'm sorry if it seems like I'm really pushing lightdm on you, the reader, but it's the display manager I have had the absolute best results with throughout the years.

As always, email me with questions at nickolas@nliaquin.xyz
