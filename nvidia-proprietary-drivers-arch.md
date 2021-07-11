# Installing Proprietary Nvidia Drivers on Arch
In this guide, we'll cover how to improve your Nvidia GPU performance on Arch by installing the proprietary drivers and making some optimizations. Read carefully, take it slow, copy and paste if needed, and try to understand what you're doing as you follow along so that any necessary troubleshooting isn't too lengthy or difficult afterwards.

First we need to modify pacman's configuration file in a text editor with root privileges, ex. vim, nano:
> sudo vim /etc/pacman.conf

Find a commented out section that looks like the following and remove the pound signs to uncomment
```bash
#[multilib]
#Include = /etc/pacman.d/mirrorlist
```

Then update your pacman databases:
> sudo pacman -Syy

Now create a directory in /etc/pacman.d/ called hooks:
> sudo mkdir /etc/pacman.d/hooks

And then use a text editor to create a file called nvidia.hook in the new directory:
> sudo vim /etc/pacman.d/hooks/nvidia.hook

And enter the following information into the text editor as is:
```bash
[Trigger]
Operation=Install
Operation=Upgrade
Operation=Remove
Type=Package
Target=nvidia
Target=linux
# Change the linux part above and in the Exec line if a different kernel is used

[Action]
Description=Update Nvidia module in initcpio
Depends=mkinitcpio
When=PostTransaction
NeedsTargets
Exec=/bin/sh -c 'while read -r trg; do case $trg in linux) exit 0; esac; done; /usr/bin/mkinitcpio -P'
```

This way we can create a rule list for Nvidia drivers to prevent downloading the wrong drivers.

Now create a file in /etc/modprobe.d called blacklist-nvidia-nouveau.conf with root privileges to block the open source drivers:
> sudo vim /etc/modprobe.d/blacklist-nvidia-nouveau.conf

and enter the following text into the open file:
```bash
blacklist nouveau
```

Now we need to create a configuration file for Nvidia cards on Xorg:
> sudo vim /etc/X11/xorg.conf.d/20-nvidia.conf

and enter the following text:
```bash
Section "OutputClass"
Identifier "intel"
MatchDriver "i915"
Driver "modesetting"
EndSection

Section "OutputClass"
Identifier "nvidia"
MatchDriver "nvidia-drm"
Driver "nvidia"
Option "AllowEmptyInitialConfiguration"
Option "PrimaryGPU" "yes"
ModulePath "/usr/lib/nvidia/xorg"
ModulePath "/usr/lib/xorg/modules"
EndSection
```

Now let's set it to where the Nvidia modules load on boot every time by modifying an existing file called mkinitcpio.conf:
> sudo vim /etc/mkinitcpio.conf

Look for a section up top that looks like this:
> MODULES=()

Change it to look like this:
> MODULES=(nvidia nvidia_modeset nvidia_uvm nvidia_drm)

Then use the following command to run said changes:
> sudo mkinitcpio -P linux

Now modify you xinitrc file:
> vim ~/.xinitrc

and add the following at the end:
```bash
xrandr --setprovideroutputsource modesetting NVIDIA-0
xrandr --auto
exec i3 &>> "/var/log/i3.log"
```

Now install the necessarry packages for Nvidia on Arch:
> sudo pacman -S lib32-mesa vulkan-radeon lib32-vulkan-radeon vulkan-icd-loader lib32-vulkan-icd-loader nvidia nvidia-settings nvidia-utils nvtop

While we're at it, let's install Wine and Lutris since I know you're here for gaming reasons, most likely:
> sudo pacman -S wine-staging giflib lib32-giflib libpng lib32-libpng libldap lib32-libldap gnutls lib32-gnutls mpg123 lib32-mpg123 openal lib32-openal v4l-utils lib32-v4l-utils libpulse lib32-libpulse libgpg-error lib32-libgpg-error alsa-plugins lib32-alsa-plugins alsa-lib lib32-alsa-lib libjpeg-turbo lib32-libjpeg-turbo sqlite lib32-sqlite libxcomposite lib32-libxcomposite libxinerama lib32-libgcrypt libgcrypt lib32-libxinerama ncurses lib32-ncurses opencl-icd-loader lib32-opencl-icd-loader libxslt lib32-libxslt libva lib32-libva gtk3 lib32-gtk3 gst-plugins-base-libs lib32-gst-plugins-base-libs vulkan-icd-loader lib32-vulkan-icd-loader lutris

Finally, reboot, install a game, open a terminal and run the nvtop command, run said game, and verify that the game shows up on the list of nvtop processes. If it did, you successfully installed the proper drivers and did everything right! I know this was a long process, and maybe even too long for what we're trying to accomplish, but getting the best Nvidia performance is not an easy task on Linux due to a lack of support for open driver tools from the company. Nvidia doesn't give the insight that AMD does to Linux users, so consider making your next GPU an AMD. As always, do research on the individual card first, as there have been AMD cards not well supported on Linux as well. Use your judgement, and if you need extra help, email me at nickolas@nliaquin.xyz