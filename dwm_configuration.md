# dwm Configurations
I'm just going to throw in some misc configurations that are geared towards dwm and other desktop-environment free settings.


### Setting GTK Themes and Icons
First you need to place your themes into `/usr/share/themes` and your icons into `/usr/share/icons`.

Then you need to create a file called settings.ini in .config/gtk-3.0 with root privileges
> sudo vim .config/gtk-3.0/settings.ini

Inside the file, include the following lines:
```bash
[Settings]
gtk-theme-name = theme name
gtk-icon-theme-name = icon pack name
```

Just remember to replace **theme name** and **icon pack name** with whatever you chose as your themes and icons, and restart your application to see the changes go into affect.


### Background Image
You're going to want to use feh for this. On all package managers, the package is simply named **feh**.
In your startup scripts file, include the following command to have feh always set the background on startup:
> feh --bg-scale **/path/to/image**


### Volume Control Keys
In your config.def.h file, you should add this line to the very top, where all namespace imports should go:
`#include <X11/XF86keysym.h>`

This is giving you access to non-standard keys like the volume keys.

If you're using alsa-utils to control your audio, include the following declarations where you would put other cahracter arrays:
```c
static const char *volup[] = { "amixer", "set", "Master", "5%+", "unmute", NULL };
static const char *voldn[] = { "amixer", "set", "Master", "5%-", "unmute", NULL };
static const char *volmt[] = { "amixer", "set", "Master", "toggle", NULL };
```

If you're using pulseaudio, use this instead:
```c
static const char *volup[] = { "/usr/bin/pactl", "set-sink-volume", "0", "+5%", NULL };
static const char *voldn[] = { "/usr/bin/pactl", "set-sink-volume", "0", "-5%", NULL };
static const char *volmt[] = { "/usr/bin/pactl", "set-sink-mute", "0", "toggle", NULL };
```

Finally, you're going to add the following lines in the keys array object:

```c
{ 0, XF86XK_AudioRaiseVolume, spawn, {.v = volup } },
{ 0, XF86XK_AudioLowerVolume, spawn, {.v = voldn } },
{ 0, XF86XK_AudioMute, spawn, {.v = volmt } },
```
