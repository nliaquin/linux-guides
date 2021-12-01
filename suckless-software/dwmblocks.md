# suckless slock Installation and Configuration
Please be sure to have completed the **dwm.md**, **dmenu.md**, and **xorg-config.md** guides before following this guide.

Now that you've made it this far with installing and configuring dwm, you likely have already noticed there is a small string of text in the top-right corner. This is the version of dwm you're running, and it's not very useful. This guide will show you how to add a patch called dwmblocks that will allow you to configure this string very easily and conveniently.

First, go into your suckless directory where you store the other suckless software and git clone the following:
```bash
git clone https://github.com/torrinfail/dwmblocks
```

You should have a folder called dwmblocks now, cd into it:
```bash
cd dwmblocks
```

Now use a text editor on a file called **blocks.def.h** and you'll see the following:
```c
//Modify this file to change what commands output to your statusbar, and recompile using the make command.
static const Block blocks[] = {
        /*Icon*/        /*Command*/             /*Update Interval*/     /*Update Signal*/
        {"Mem:", "free -h | awk '/^Mem/ { print $3\"/\"$2 }' | sed s/i//g",     30,             0},

        {"", "date '+%b %d (%a) %I:%M%p'",                                      5,              0},
};

//sets delimeter between status commands. NULL character ('\0') means no delimeter.
static char delim[] = " | ";
static unsigned int delimLen = 5;
```

This is just a sample of what you can do. The maintainer of dwmblocks gives you free memory and date. You'll notice entries are seperated into character arrays within an array. Also make note that the delimeter between commands is set to **|** as defined by the delim[] character array. If you compile as is, the output in the top right corner on next startup will look like this:
> Mem: 300M/12G | Jun 30 (Mon) 11:30AM

You can add all sorts of output with commands you would normally run in the terminal. For instance, all hardware on your machine is looked at by linux closely, and all data from hardware are written to files in your /sys/class/ directory. If you have a laptop, you can get how much battery is left just by printing out /sys/class/power_supply/BAT0/status like so:
```bash
cat /sys/class/power_supply/BAT0/status
```

So now, you can enter this information into the blocks array like so:
```c
{"", "cat /sys/class/power_supply/BAT0/status", 5, 0},
```

You may also be wondering what the numbers on the right mean. The first one is how often (in seconds) and the second is the update signal which is dependent on the process or program itself changing on a particular signal. Some of your entries into blocks.def.h will not update simply with a defined interval, like volume indicators. You would have to define a signal for that and then enter the signal value into the *Update Signal* field. Come up with some of the more important indicators you would like in the corner and look up how to get those results with the terminal online. If you can do it in the terminal, you can do it in dwmblocks.

Once you've finished configuring, make sure you don't have a file called blocks.h (delete it if you do) and run the following command:
```bash
sudo make clean install
```

If nothing fails, you should see the following:
```bash
[user@distro dwmblocks]$ sudo make clean install
[sudo] password for user: 
rm -f *.o *.gch dwmblocks
cc  dwmblocks.c -lX11 -o dwmblocks
mkdir -p /usr/local/bin
install -m 0755 dwmblocks /usr/local/bin/dwmblocks
```

And that's it. Please feel free to email for help via nickolas@nliaquin.xyz
