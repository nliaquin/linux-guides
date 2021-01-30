# Suckless dwm Installation & Configuration
*Propritaries:* Debian-Based Distros
*Dependencies:* Git, Xorg, and several devtools that will be listed below
*Difficulty:* Moderately Difficult

### Preface
I made this as my first guide on here and removed my dwm.sh script in my apt-scripts repo, as well as my dwm repo, for a few reasons.

- I removed my dwm.sh script because it doesn't always work correctly depending on the distro. Everything I wrote in it is *correct* for the majority of Debian-based distros, the problem is that, on a distro like Ubuntu, I did not realize you have to reboot before removing gdm3, set up .xinitrc and .bash_profile, reboot again, and then you can start to install and configure dwm. On the three distros I have on my machines, typically you can just run that script once, reboot one time, and everything works. I discovered Pop OS has this problem, too. I'm typically a Debian and Kali user, so I don't always know what's going to happen on specific distros.

- I now have multiple dwm configs for specific machines. On my desktop, I limit my tags to three per screen with three monitors total. On my laptops, I like to have six to nine tags. On one of my machines, I prefer the Super key to be Ctrl, and on my other machines it's the Windows key. This is why I got rid of my dwm repo, there's just no point. I may as well just git clone from suckless and customize from scratch.

- I wanted to create a guide that just works on any Debian-based distro. You should pull this guide up on a phone or other device, because you're going to be rebooting a few times to make sure this goes right.

### Installing Suckless dwm

# Guide in progress
