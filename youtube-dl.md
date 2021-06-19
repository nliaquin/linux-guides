# Youtube DL Installation and Usage
In this guide, I'm going to quickly show you how to install and use Youtube DL

### Installation
Debian-Based Distros:
> sudo apt install youtube-dl

RedHat-Based Distros:
> sudo yum install youtube-dl

Arch-Based Distros:
> sudo pacman -S youtube-dl

If your distro doesn't have a repository with youtube-dl available, this is because some maintainers have chosen to exclude it for one reason or another. This next method will work on any distro, or even Windows, so long as you have Python or Python3 installed.

First, don't just assume you already have python3 or pip, check:
> which python3

If this returns the location of python3 on your filesystem, example **/usr/bin/python3**, then you already have Python3.

If it returns **which: no python3 in (/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl)**, then you need to install Python3 ...
Debian-Based Distros:
> sudo apt install python3

RedHat-Based Distros:
> sudo yum install python3

Arch-Based Distros:
> sudo pacman -S python

Now check if you have pip like so:
> which pip

If this returns **which: no pip in (/usr/local/bin:/usr/bin:/bin:/usr/local/sbin:/usr/bin/site_perl:/usr/bin/vendor_perl:/usr/bin/core_perl)**, install it like so...
Debian-Based Distros:
> sudo apt install pip

RedHat-Based Distros:
> sudo yum install pip

Arch-Based Distros:
> sudo pacman -S python-pip

Finally, let's use pip to install youtube-dl:
> python -m pip install youtube_dl

Now to use youtube-dl this way, you need to type the following:
> python3 -m youtube_dl

But you should modify your .bashrc to include the following alias at the end:
> alias youtube-dl="python3 -m youtube_dl"

### Usage
To generally use youtube-dl, all you have to do is:
> youtube-dl **link to video**

However, this just grabs a video without considering the export format, title, playlist, etc. You'll notice that if you download a video using the method above, you'll get a video format that youtube-dl considered to be the best for exporting, and you'll have a random string of characters at the end of the title before the extention.

To download a video with the exact title it has, meaning no added characters, you can use one of two arguments below, or both...
Using title specifiers:
> youtube-dl -o '%(title)s%(ext)s' **link to video**

Using --restrict-filenames:
> youtube-dl **link to video** --restrict-filenames

Using both:
> youtube-dl -o '%(title)s%(ext)s' **link to video** --restrict-filenames

The last method is usually best suited in the case of the video title have special character, emoticons, etc.

You can also download playlists with youtube-dl by doing the following:
> youtube-dl -o '%(playlist_index)s-%(title)s%(ext)s' **link to playlist**

If you want to control the format you export, you can use the -F command to see what formats are available:
> youtube-dl -F **link to video**

This will output something like the following...

``139          m4a        audio only DASH audio   52k , m4a_dash container, mp4a.40.5 (22050Hz), 15.37MiB``

``251          webm       audio only tiny  112k , webm_dash container, opus @112k (48000Hz), 35.32MiB``

``140          m4a        audio only tiny  129k , m4a_dash container, mp4a.40.2@129k (44100Hz), 40.78MiB``

``278          webm       256x144    DASH video   95k , webm_dash container, vp9, 30fps, video only``

``160          mp4        256x144    DASH video  108k , mp4_dash container, avc1.4d400b, 30fps, video only``

``242          webm       426x240    DASH video  220k , webm_dash container, vp9, 30fps, video only``

``133          mp4        426x240    DASH video  242k , mp4_dash container, avc1.4d400c, 30fps, video only``

``134          mp4        640x360    360p   85k , mp4_dash container, avc1.4d401e@  85k, 30fps, video only, 26.78MiB``

``243          webm       640x360    DASH video  405k , webm_dash container, vp9, 30fps, video only``

``244          webm       854x480    DASH video  752k , webm_dash container, vp9, 30fps, video only``

``135          mp4        854x480    DASH video 1155k , mp4_dash container, avc1.4d4014, 30fps, video only``

``247          webm       1280x720   DASH video 1505k , webm_dash container, vp9, 30fps, video only``

``136          mp4        1280x720   DASH video 2310k , mp4_dash container, avc1.4d400b, 30fps, video only``

``137          mp4        1920x1080  1080p  498k , mp4_dash container, avc1.640028@ 498k, 30fps, video only, 157.15MiB``

``248          webm       1920x1080  DASH video 2646k , webm_dash container, vp9, 30fps, video only``

``18           mp4        640x360    360p  199k , avc1.42001E, 30fps, mp4a.40.2 (44100Hz), 62.74MiB``

``22           mp4        1280x720   720p  402k , avc1.64001F, 30fps, mp4a.40.2 (44100Hz) (best)``

The number furthest to the left is the format number. Notice the second column is the available formats corresponding to the format number. You could download a video as an mp3 or m4a for audio only. Pay close attention to the far right where some formats say *video only*. To state the abvious, this means there is no audio for this format.

To control for which format you want, you need to replace -F with -f**format number**. Example, I want the audio only:
> youtube-dl -f140 **link to video**

You can combine all of the above commands if desired like so:
> youtube-dl -f140 -o '%(playlist_index)s-%(title)s%(ext)s' **link to video** --restrict-filenames

### Conclusion
Since there are nearly a hundred possible ways to use youtube-dl, I'm going to cut this guide here and let you use the man pages to read more:
> man youtube-dl

Or check out the wiki.
