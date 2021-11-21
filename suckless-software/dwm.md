# suckless dwm Installation and Configuration
When installing suckless software, please be sure to start with this guide (dwm.md) or the dmenu guide (dmenu.md) before you move onto any other suckless guide. The other guides won't work until you start with this one or the dmenu one!


### Table of Contents
 - [Preliminaries](#preliminaries)
 - [Downloading suckless dwm](#downloading-suckless-dwm)
 - [Configuring suckless dwm](#configuring-suckless-dwm)


### Preliminaries
You are going to need to install the following software:
 - git
 - xorg
 - build-essential (for Debian-based distros) | base-devel (for arch-based distros)

Run the following if you are on a Debian-based distro:
```bash
sudo apt install build-essential git xorg libx11-dev libxinerama-dev libxft-dev libwebkit2gtk-4.0-dev
```

Run the following if you are on an Arch-based distro:
```bash
sudo pacman -S xorg xorg-xinit base-devel
```

### Downloading suckless dwm
After installing the necessary packages, go ahead and create a folder in your $HOME directory where you will want to keep all suckless files. (eg: ~/.suckless)

Go into the directory you created and type in the following command:
```bash
git clone https://git.suckless.org/dwm
```

Once dwm is finished downloading, go into the directory:
```bash
cd dwm/
```

### Configuring suckless dwm
There are many configuration options for dwm, including options to change keybord shortcuts, add definitions to spawn programs with custom keyboard shortcuts, etc. Below, I will go over some examples, but just know that all customizations to your dwm configuration are done through editing the config.def.h file. Use your preferred text editor to modify config.def.h now.

You'll notice at the very top of the file the following:
```c
/* appearance */
static const unsigned int borderpx  = 1;        /* border pixel of windows */
static const unsigned int snap      = 32;       /* snap pixel */
static const int showbar            = 1;        /* 0 means no bar */
static const int topbar             = 1;        /* 0 means bottom bar */
static const char *fonts[]          = { "monospace:size=10" };
static const char dmenufont[]       = "monospace:size=10";
static const char col_gray1[]       = "#222222";
static const char col_gray2[]       = "#444444";
static const char col_gray3[]       = "#bbbbbb";
static const char col_gray4[]       = "#eeeeee";
static const char col_cyan[]        = "#005577";
static const char *colors[][3]      = {
        /*               fg         bg         border   */
        [SchemeNorm] = { col_gray3, col_gray1, col_gray2 },
        [SchemeSel]  = { col_gray4, col_cyan,  col_cyan  },
};
```

This is where you can choose to modify the font, colors, and other appearance options of dwm. Let's say you want to change the font; all you'd have to do is change **monospace** to whatever your prefered font is. This font is case sensitive, so if your font is Arial with a capital A, it has to be written like that. Also, remember that if the font does not exist in **/usr/share/fonts/** or **/usr/local/share/fonts** or **~/.fonts**, then dwm will default to whatever the base font your Linux distro came with.

Another example of a section you might want to change is the *commands* section:
```c
/* commands */
static char dmenumon[2] = "0"; /* component of dmenucmd, manipulated in spawn() */
static const char *dmenucmd[] = { "dmenu_run", "-m", dmenumon, "-fn", dmenufont, "-nb", col_gray1, "-nf", col_gray3, "-sb", col_cyan, "-sf", col_gray4, NULL };
static const char *termcmd[]  = { "st", NULL };

static Key keys[] = {
        /* modifier                     key        function        argument */
        { MODKEY,                       XK_p,      spawn,          {.v = dmenucmd } },
        { MODKEY|ShiftMask,             XK_Return, spawn,          {.v = termcmd } },
        { MODKEY,                       XK_b,      togglebar,      {0} },
        { MODKEY,                       XK_j,      focusstack,     {.i = +1 } },
        { MODKEY,                       XK_k,      focusstack,     {.i = -1 } },
        { MODKEY,                       XK_i,      incnmaster,     {.i = +1 } },
        { MODKEY,                       XK_d,      incnmaster,     {.i = -1 } },
        { MODKEY,                       XK_h,      setmfact,       {.f = -0.05} },
        { MODKEY,                       XK_l,      setmfact,       {.f = +0.05} },
        { MODKEY,                       XK_Return, zoom,           {0} },
        { MODKEY,                       XK_Tab,    view,           {0} },
        { MODKEY|ShiftMask,             XK_c,      killclient,     {0} },
        { MODKEY,                       XK_t,      setlayout,      {.v = &layouts[0]} },
        { MODKEY,                       XK_f,      setlayout,      {.v = &layouts[1]} },
        { MODKEY,                       XK_m,      setlayout,      {.v = &layouts[2]} },
        { MODKEY,                       XK_space,  setlayout,      {0} },
        { MODKEY|ShiftMask,             XK_space,  togglefloating, {0} },
        { MODKEY,                       XK_0,      view,           {.ui = ~0 } },
        { MODKEY|ShiftMask,             XK_0,      tag,            {.ui = ~0 } },
        { MODKEY,                       XK_comma,  focusmon,       {.i = -1 } },
        { MODKEY,                       XK_period, focusmon,       {.i = +1 } },
        { MODKEY|ShiftMask,             XK_comma,  tagmon,         {.i = -1 } },
        { MODKEY|ShiftMask,             XK_period, tagmon,         {.i = +1 } },
        TAGKEYS(                        XK_1,                      0)
        TAGKEYS(                        XK_2,                      1)
        TAGKEYS(                        XK_3,                      2)
        TAGKEYS(                        XK_4,                      3)
        TAGKEYS(                        XK_5,                      4)
        TAGKEYS(                        XK_6,                      5)
        TAGKEYS(                        XK_7,                      6)
        TAGKEYS(                        XK_8,                      7)
        TAGKEYS(                        XK_9,                      8)
        { MODKEY|ShiftMask,             XK_q,      quit,           {0} },
};
```

There are a lot of keybinding options already set for you, but some of these options may not be to your liking. All keys are from the xkey package, but not all keys are included in that library. To add more key options for special keys, you'll want to add the following line of code either at the very top of this file, or just below /* commands */:
```c
#include <X11/XF86keysym.h>
```

This library gives you access to XF86XK keys like volume up (XF86XK_AudioRaiseVolume), volume down (XF86XK_AudioLowerVolume), brightness up (XF86XK_MonBrightnessUp), brightness down (XF86XK_MonBrightnessDown), etc.

Another thing to pay attention to is that every binding has the *MODKEY* included, either with or without a *ShiftMask*. Every dwm shortcut is initiated by holding down a modifier key and pressing another key either with or without holding down shift. For example, **{ MODKEY|ShiftMask, XK_Return, spawn, {.v = termcmd } },** means that when you hold your *Modifier Key* with *Shift* and press *Enter*, you will open up the terminal program defined by the *termcmd* variable. The *termcmd* variable was defined by the following line: 
```c
static const char *termcmd[] = { "st", NULL };
```

The *"st"* part is where you'll want to change it to whichever terminal you prefer. Currently it is st (simple temrinal), but you can download whatever terminal you want and change this to that instead, like xterminal, qterminal, etc.

You may also be wondering, what is my *modifier key* by default? That would be defined above in a section called *key definitions*:
```c
/* key definitions */
#define MODKEY Mod1Mask
#define TAGKEYS(KEY,TAG) \
        { MODKEY,                       KEY,      view,           {.ui = 1 << TAG} }, \
        { MODKEY|ControlMask,           KEY,      toggleview,     {.ui = 1 << TAG} }, \
        { MODKEY|ShiftMask,             KEY,      tag,            {.ui = 1 << TAG} }, \
        { MODKEY|ControlMask|ShiftMask, KEY,      toggletag,      {.ui = 1 << TAG} },
```

*Mod1Mask* means the *Alt key*. I set my *modifier key* to the *Windows key*. You can do the same by changing *Mod1Mask* to *Mod4Mask*

Another thing you can do is add keybindings for other programs. If you want to open a specific program, let's say firefox for example, first make a definition for it underneath where you saw the definition for simple terminal:
```c
static const char *termcmd[] = { "st", NULL };
static const char *firefox[] = { "firefox", NULL };
```

Just to explain a little to better convey what's going on here, you are defining a constant character array by the name of firefox to store two arguments, the name of the command to open the program which in this case is *firefox*, and a NULL which defines the end of the character array. That's just how strings in C work, technically a character array is just a string, and in most languages, NULL or NOTHING can be found at the very end of every string. You can name the character array anything you want, it doesn't have to be named *firefox*. You could have even named it *ff* like so:
```c
static const char *ff[] = { "firefox", NULL };
```

As you continue through this guide series, you'll find more information on changing your keybindings when you patch in more suckless programs.


### Compiling suckless dwm
When you're finished modifying **config.def.h**, save and close the file. Before you compile, always perform an *ls* command and make sure you do not see a file named **config.h**. This is very important, because if **config.h** exists, none of your changes to **config.def.h** will compile. Always delete **config.h** when you go to compile changes you made in **config.def.h**. Compiling with **config.h** present in your **dwm** folder may result in **config.def.h** being modified, which may overwrite your changes.

Now that I've hammered in that you need to delete **config.h**, let's compile!
```bash
sudo make clean install
```

If all was done write, the output should look something like this:
```bash
[user@distro dwm]$ sudo make clean install
[sudo] password for user: 
rm -f dwm drw.o dwm.o util.o dwm-6.2.tar.gz
dwm build options:
CFLAGS   = -std=c99 -pedantic -Wall -Wno-deprecated-declarations -Os -I/usr/X11R6/include -I/usr/include/freetype2 -D_DEFAULT_SOURCE -D_BSD_SOURCE -D_POSIX_C_SOURCE=200809L -DVERSION="6.2" -DXINERAMA
LDFLAGS  = -L/usr/X11R6/lib -lX11 -lXinerama -lfontconfig -lXft
CC       = cc
cp config.def.h config.h
cc -c -std=c99 -pedantic -Wall -Wno-deprecated-declarations -Os -I/usr/X11R6/include -I/usr/include/freetype2 -D_DEFAULT_SOURCE -D_BSD_SOURCE -D_POSIX_C_SOURCE=200809L -DVERSION=\"6.2\" -DXINERAMA drw.c
cc -c -std=c99 -pedantic -Wall -Wno-deprecated-declarations -Os -I/usr/X11R6/include -I/usr/include/freetype2 -D_DEFAULT_SOURCE -D_BSD_SOURCE -D_POSIX_C_SOURCE=200809L -DVERSION=\"6.2\" -DXINERAMA dwm.c
cc -c -std=c99 -pedantic -Wall -Wno-deprecated-declarations -Os -I/usr/X11R6/include -I/usr/include/freetype2 -D_DEFAULT_SOURCE -D_BSD_SOURCE -D_POSIX_C_SOURCE=200809L -DVERSION=\"6.2\" -DXINERAMA util.c
cc -o dwm drw.o dwm.o util.o -L/usr/X11R6/lib -lX11 -lXinerama -lfontconfig -lXft
mkdir -p /usr/local/bin
cp -f dwm /usr/local/bin
chmod 755 /usr/local/bin/dwm
mkdir -p /usr/local/share/man/man1
sed "s/VERSION/6.2/g" < dwm.1 > /usr/local/share/man/man1/dwm.1
chmod 644 /usr/local/share/man/man1/dwm.1
```

Otherwise, this output will contain lines saying *error* with information as to what went wrong. These errors vary, but they are straightforward and will tell you what line of code in **config.def.h** caused the error with suggestions as to how you can fix it. This is where it comes in handy to know a little C by the way.

If you've finished this guide and the guide on compiling dmenu, you're ready for the patch guides. If you had any trouble, feel free to email me via nickolas@nliaquin.xyz
