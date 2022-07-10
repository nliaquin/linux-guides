# How to change your keyboard language
### specifically if you are using X11, not Wayland.

For this guide, we'll assume your default display manager is xorg. These instructions will guide you on how to use different keyboard layouts on any linux distro, assuming you are using xorg and not any other display manager. Once you learn how to do it this way, however, learning how to do it for Wayland or another display manager is pretty easy.

First, you'll need to create and edit a file in the X11 configuration directory as root:
```bash
sudo vim /etc/X11/xorg.conf.d/00-keyboard.conf
```

Here you will enter some information that indicates to xorg what your keyboard language will be after initialization every time your computer starts up.
```bash
Section "InputClass"
	Identifier "system-keyboard"
	MatchIsKeyboard "on"
	Option "XkbLayout" "us"
EndSection
```

This information indicated that the keyboard layout will be a United States layout. To see all the options for layouts, enter the following command into a terminal:
```bash
localectl list-x11-keymap-layouts
```

You will get a long list of all available layouts on your distro. If you wanted an Italian keyboard layout, your configuration file would like like this:
```bash
Section "InputClass"
        Identifier "system-keyboard"
        MatchIsKeyboard "on"
        Option "XkbLayout" "it"
EndSection
```

And of course, since this is a file that is only read upon the init system awakening the xorg program, there are only two ways to put the change into effect: restart your computer, or restart xorg. Honestly, it's easier just to restart your computer. Restarting xorg depends on you targeting the specific display environment you have, and if you're a newer user who is using a distro with an easy installer, you might not know which of the seven different mainstream programs you have running.

I hope this guide helps, but if you need further assistance, I can be reached via email at nickolas@nliaquin.xyz
