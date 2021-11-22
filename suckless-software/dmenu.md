# suckless dmenu Installation and Configuration
Please be sure to complete this guide, the **dwm.md** guide, and the **xorg-config.md** guide before moving onto any other guide in **suckless-software**.


### Table of Contents
 - [Preliminaries](#preliminaries)
 - [Downloading suckless dmenu](#downloading-suckless-dmenu)
 - [Compiling suckless dmenu](#compiling-suckless-dmenu)


### Preliminaries
You're going to need the following software:
 - git
 - build-essential (for Debian-based distros) | base-devel (for arch-based distros)

Run the following if you are on a Debian-based distro:
```bash
sudo apt install git build-essential
```

Run the following if you are on an Arch-based distro:
```bash
sudo pacman -S git base-devel
```

### Downloading suckless dmenu
After installing the necessary packages, go ahead and create a folder in your $HOME directory, if you haven't already, where you will want to keep all suckless files. (eg: ~/.suckless)

Go into the directory you created and type in the following command:
```bash
git clone https://git.suckless.org/dmenu
```

Once dmenu is finished downloading, go into the directory:
```bash
cd dmenu/
```


### Compiling suckless dmenu
Since dmenu is as good as it comes, there is no need for configuration, only compilation:
```bash
sudo make clean install
```

If all was successful, this is what the output should look like:
```bash
[user@distro dmenu]$ sudo make clean install
[sudo] password for user: 
rm -f dmenu stest drw.o dmenu.o stest.o util.o dmenu-5.0.tar.gz
dmenu build options:
CFLAGS   = -std=c99 -pedantic -Wall -Os -I/usr/X11R6/include -I/usr/include/freetype2 -D_DEFAULT_SOURCE -D_BSD_SOURCE -D_XOPEN_SOURCE=700 -D_POSIX_C_SOURCE=200809L -DVERSION="5.0" -DXINERAMA
LDFLAGS  = -L/usr/X11R6/lib -lX11 -lXinerama -lfontconfig -lXft
CC       = cc
cc -c -std=c99 -pedantic -Wall -Os -I/usr/X11R6/include -I/usr/include/freetype2 -D_DEFAULT_SOURCE -D_BSD_SOURCE -D_XOPEN_SOURCE=700 -D_POSIX_C_SOURCE=200809L -DVERSION=\"5.0\" -DXINERAMA dmenu.c
dmenu.c: In function ‘setup’:
dmenu.c:24:30: warning: ‘*’ in boolean context, suggest ‘&&’ instead [-Wint-in-bool-context]
   23 | #define INTERSECT(x,y,w,h,r)  (MAX(0, MIN((x)+(w),(r).x_org+(r).width)  - MAX((x),(r).x_org)) \
      |                               ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
   24 |                              * MAX(0, MIN((y)+(h),(r).y_org+(r).height) - MAX((y),(r).y_org)))
      |                              ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
dmenu.c:648:37: note: in expansion of macro ‘INTERSECT’
  648 |                                 if (INTERSECT(x, y, 1, 1, info[i]))
      |                                     ^~~~~~~~~
cc -c -std=c99 -pedantic -Wall -Os -I/usr/X11R6/include -I/usr/include/freetype2 -D_DEFAULT_SOURCE -D_BSD_SOURCE -D_XOPEN_SOURCE=700 -D_POSIX_C_SOURCE=200809L -DVERSION=\"5.0\" -DXINERAMA drw.c
cc -c -std=c99 -pedantic -Wall -Os -I/usr/X11R6/include -I/usr/include/freetype2 -D_DEFAULT_SOURCE -D_BSD_SOURCE -D_XOPEN_SOURCE=700 -D_POSIX_C_SOURCE=200809L -DVERSION=\"5.0\" -DXINERAMA util.c
cc -o dmenu dmenu.o drw.o util.o -L/usr/X11R6/lib -lX11 -lXinerama -lfontconfig -lXft
cc -c -std=c99 -pedantic -Wall -Os -I/usr/X11R6/include -I/usr/include/freetype2 -D_DEFAULT_SOURCE -D_BSD_SOURCE -D_XOPEN_SOURCE=700 -D_POSIX_C_SOURCE=200809L -DVERSION=\"5.0\" -DXINERAMA stest.c
cc -o stest stest.o -L/usr/X11R6/lib -lX11 -lXinerama -lfontconfig -lXft
mkdir -p /usr/local/bin
cp -f dmenu dmenu_path dmenu_run stest /usr/local/bin
chmod 755 /usr/local/bin/dmenu
chmod 755 /usr/local/bin/dmenu_path
chmod 755 /usr/local/bin/dmenu_run
chmod 755 /usr/local/bin/stest
mkdir -p /usr/local/share/man/man1
sed "s/VERSION/5.0/g" < dmenu.1 > /usr/local/share/man/man1/dmenu.1
sed "s/VERSION/5.0/g" < stest.1 > /usr/local/share/man/man1/stest.1
chmod 644 /usr/local/share/man/man1/dmenu.1
chmod 644 /usr/local/share/man/man1/stest.1
```

Ignore the warnings, you only have to worry if you see errors. That's it for this guide, and if you haven't already, go complete the **dwm.md** and **xorg-config.md** guides.

If you had any problems or have any questions, email me via nickolas@nliaquin.xyz
