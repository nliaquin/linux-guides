# Changing Mouse Sensitivity
### through X11...

Disclaimer, this guide does not work if you use anything other than **xorg** as your display manager. I'm making this guide for those who use xorg, but don't use desktop enviroments which provide GUI tools to adjust things like mouse sensitivity. Most people use xorg, and most people don't realize that OS environment variables such as your mouse sensitivity is set within the xorg environment. There is no *pure Linux* way of going about this. Without a display manager such as xorg, we have no mouse to begin with. Keep this in mind, and think about this when you start to wonder what other environmental sandbox variables are provided through and set by.

First, you need to use a text editor to create and edit a file in */etc/X11/xorg.conf.d/*, as sudo of course, and call it *90-libinput.conf*:
```bash
sudo vim /etc/X11/xorg.conf.d/90-libinput.conf
```

Then enter the following information:
```bash
Section "InputClass"
        Identifier "Default Pointer (libinput)"
        MatchDriver "libinput"
        MatchIsPointer "on"
        Option "AccelSpeed" "-0.7"
EndSection
```

Briefly, we're defining a class to modify our input device settings permanently. The *default pointer* should obviously be your mouse. *libinput* is what's used to define your mouse driver on Linux. *MatchIsPointer* just checks to make sure it's a pointer device, or in lamens terms, a mouse. Lastly, we define the sensitivity through the *AccelSpeed* option. It's important that you wrap both *AccelSpeed* **and** the value afterward in quotes. In my case, the mouse sensitivity value is -0.7. I need to wrap this in quotes because giving a value that is not an integer when defining it spits out an error. It's just how c works.

That's it. I hope it was helpful, as it's a lot simpler than having to actually download a whole mouse program for this one setting. It was a quick one, and you should check out what other sandbox variables you can mess with in xorg.
