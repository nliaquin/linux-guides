### dwm Configuration

#### Volume Keys
Before configuring volume keys, I must preface, I am a user of alsa-utils. If you want this to work, you will need to install alsa-utils.

For aptitude package manager:

> sudo apt install alsa-utils

For pacman package manager:

> sudo pacman -Sy alsa-utils


In your config.def.h file, you should add this line to the very top, where all namespace imports should go:

`#include <X11/XF86keysym.h>`

Where the other character arrays are declared in the commands section, create the following arrays:

`static const char *volup[] = { "amixer", "set", "Master", "5%+", "unmute", NULL };
static const char *voldn[] = { "amixer", "set", "Master", "5%-", "unmute", NULL };
static const char *volmt[] = { "amixer", "set", "Master", "toggle", NULL };`

Finally, you're going to add the following lines in the keys array object:

`{ 0, XF86XK_AudioRaiseVolume, spawn, {.v = volup } },
{ 0, XF86XK_AudioLowerVolume, spawn, {.v = voldn } },
{ 0, XF86XK_AudioMute, spawn, {.v = volmt } },`
