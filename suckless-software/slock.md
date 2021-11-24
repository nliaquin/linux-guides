# suckless slock Installation and Configuration
Please be sure to have completed the **dwm.md**, **dmenu.md**, and **xorg-config.md** guides before following this guide.

Now that you've made it this far with installing and configuring dwm, something you should be aware of is that there is no lockscreen, only logout. That is, until you install slock. Unlike display managers, dwm does not come with a utility by default. Thankfully, someone came along and made *slock*, a very simple lockscreen.

First, go into your suckless directory where you store the other suckless software and git clone the following:
```bash
git clone https://git.suckless.org/slock
```

You should have a folder called slock now, cd into it:
```bash
cd slock
```

Now use a text editor on a file called **config.def.h** and you'll see the following:
```c
/* user and group to drop privileges to */
static const char *user  = "nobody";
static const char *group = "nogroup";

static const char *colorname[NUMCOLS] = {
        [INIT] =   "black",     /* after initialization */
        [INPUT] =  "#005577",   /* during input */
        [FAILED] = "#CC3333",   /* wrong password */
};

/* treat a cleared input like a wrong password (color) */
static const int failonclear = 1;
```

The one thing you should modify is to change *nobody* to your user and *nogroup* to your user as well. After that, save your changes and exit.

Once you've finished all that, just run the following command:
```bash
sudo make clean install
```

If nothing fails, you should see the following:
```bash
[user@distro slock]$ sudo make clean install
[sudo] password for user: 
cleaning
slock build options:
CFLAGS   = -std=c99 -pedantic -Wall -Os -I. -I/usr/include -I/usr/X11R6/include -DVERSION="1.4" -D_DEFAULT_SOURCE -DHAVE_SHADOW_H
LDFLAGS  = -s -L/usr/lib -lc -lcrypt -L/usr/X11R6/lib -lX11 -lXext -lXrandr
CC       = cc
CC slock.c
CC explicit_bzero.c
CC -o slock
installing executable file to /usr/local/bin
installing manual page to /usr/local/share/man/man1
```

Now you're probably wondering how to use your new lockscreen. Simply run the command **slock**, and your screen will go black. Once you start typing your password, you'll see a blue screen indicating that it's taking in your password. Once you hit enter, you'll either see a red screen indicating you failed to log in, or you'll be brought back to dwm.

Alternatively, just add a shortcut to your dwm **config.def.h** file, ex.:
```c
static const char *lockscreen[] = { "slock", NULL };
...
static Key keys[] = {
    ...
    { MODKEY|ShiftMask, XK_l, spawn, {.v = lockscrn } },
    ...
}
```

Please feel free to email for help via nickolas@nliaquin.xyz
