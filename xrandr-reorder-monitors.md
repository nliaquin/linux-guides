# Reorder monitors with Xrandr
xrandr is a standard tool packed in nearly every Linux distro, is extremely powerful with many features, and I cannot recommned it enough that you, the reader, try to learn this and as many other standard Linux utilities as possible.

## Getting Started
Type the following in your terminal to list all your monitors and their supports resolutions:

```bash
xrandr
```

You'll notice that the more monitors you have, the more entries there will be. It might be wise to use a terminal with scrolling support built into it, like qterminal.

## Scenarios
Let's say you have two monitors. To get to the one on the left, you have to move your mouse all the way past the right side of the monitor on your right. Vice-versa for getting to the monitor on your right, you have to move your mouse all the way to the left of your left monitor to get to your right monitor.

**Confused yet?**

Let's say both of these monitors are plugged into your gpu or motherboard via HDMI cables. This means one monitor will be called *HDMI-1* and the other *HDMI-2* in the xrandr output. In the scenario I've laid out, it's likely that *HDMI-1* is on the right, and *HDMI-2* is on the left, and that's because you probably have your first monitor plugged into right-most HDMI port and you second monitor plugged in somewhere left of that. The order you plug your monitors into your gpu matters, and with two HDMI plugged monitors, the easiest fix is to plug monitor one into the left-most port of your gpu, and monitor two just to the right of it. 

But let's run a second scenario: You have one monitor that has to be plugged in with a display port cable, and the other an HDMI cable. Now xrandr will tell you that one monitor is called HDMI-1 and the other is DP-1. Let's say your DP-1 monitor is physically on the left, and you HDMI-1 monitor is physically on the right, and you don't want to move them, or can't for whatever reason. Same as before, your OS is treating the monitor on the left as if it's on the right, and vice-versa for the other monitor. We can't just replug our cables into the GPU, because it's likely that your display ports come before or after the HDMI ports.

## Reordering the monitors with xrandr

You can choose one of the following commands, and with two monitors, it wont matter which one you pick:

```bash
xrandr --output HDMI-1 --right-of DP-1
```

or

```bash
xrandr --output DP-1 --left-of HDMI-1
```


## Persisting the changes
Great, now your monitors are in the right order, but you've rebooted and the problem has returned... Well, that's because you need to write one of the above commands into a file that is executed on start.

If you use lightdm, modify your .xprofile file in your $HOME directory to include the following:

```bash
xrandr --output HDMI-1 --right-of DP-1
```

If you're using xorg with dwm, and you basically don't have a xisplay manager, modify your .xinitrc in the $HOME directory to include the following:

```bash
xrandr --output HDMI-1 --right-of DP-1
```

Now the changes will persist at startup.

That's it, and if you have any questions, email me at nickolas@nliaquin.xyz
