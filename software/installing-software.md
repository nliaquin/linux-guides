# Installing Software on Arch
### via pacman and AUR

Arch Linux provides two major ways to install software, both can be found in other distros as well. It is possible to install software the conventional way via the package manager, *pacman*, but there is also a way in which you can download a *PKGBUILD* of software which will allow you to compile the software from source. Most packages will be available via both methods, but some software will be exclusive to one or the other as well. In most cases, it is best to try and install a package using *pacman* first, and if that software is unavailable via *pacman*, then it would be best to search for it via the *Arch User Repository*, or **AUR** as it is refered to.


### Using pacman
Let's assume I want to download and install both chrome and firefox to Arch. In most cases, you'll probably know the name of the package you're looking for, but in case you don't know the name of the official package, it would be best to run a search prior to installing anything. We can search with pacman using the **-Ss** flag.
```bash
pacman -Ss firefox
```
Which should produce something like the following output:
```bash
[user@distro]$ pacman -Ss firefox
extra/dump_syms 0.0.7-1
    Symbol dumper for Firefox
extra/firefox 95.0.2-1
    Standalone web browser from mozilla.org
extra/firefox-i18n-ach 95.0.2-1
    Acholi language pack for Firefox
extra/firefox-i18n-af 95.0.2-1
    Afrikaans language pack for Firefox
extra/firefox-i18n-an 95.0.2-1
    Aragonese language pack for Firefox
extra/firefox-i18n-ar 95.0.2-1
    Arabic language pack for Firefox
extra/firefox-i18n-ast 95.0.2-1
    Asturian language pack for Firefox
extra/firefox-i18n-az 95.0.2-1
    Azerbaijani language pack for Firefox
...
```
The output spanned for pages, but this is a sample of the first several entries. As you may have noticed by the second package in the listing, firefox is available. Also, if a package is already installed, there will be an **[installed]** label next to it's entry.

Now that we know the package is just called *firefox*, we can download with the **-S** flag:
```bash
sudo pacman -S firefox
```
You'll be asked if you wish to download the following package, and once confirmed, firefox will be downloaded and installed.

If you wish to uninstall firefox, we can use the -R flag to remove the package:
```bash
sudo pacman -R firefox
```
You will be asked to confirm removal, and then it will be uninstalled.

Let's say I want chrome now, we would assume that if firefox's package is called firefox, then we can just install chrome the same way, right?
```bash
[user@distro]$ sudo pacman -S chrome
error: target not found: chrome
```
Well, as it turns out, it's not. Let's search for it: 
```bash
pacman -Ss chrome
```
This search should yield something like the following output:
```bash
[user@distro]$ pacman -Ss chrome
extra/ttf-croscore 20201226-2
    Chrome OS core fonts
extra/xf86-video-openchrome 0.6.0.r754.g15c868a-1 (xorg-drivers)
    X.Org Openchrome drivers
community/passff-host 1:1.2.3-1
    PassFF native messaging host application for Firefox, Chromium, Chrome, Vivaldi
community/python-pyppeteer 0.2.6-3
    Headless chrome/chromium automation library
community/unichrome-dri 7.11.2-11
    Mesa DRI drivers for S3 Graphics/VIA Unichrome
```
Well, it looks like chrome is not available through pacman. All is not lost, however, as several versions of chrome are available via the **AUR**.


### Using the Arch User Repository (AUR)
Installing software via the AUR is not as cut and dry as using pacman, but it can be easy. For this section, I only want to go into the basics of how to install any AUR package.

First, visit [aur.archlinux.org](#https://aur.archlinux.org/). There is a package search off to the right, just type in *google chrome*. Because a lot of packages use the word *chrome* in the title, it might be a few options down from the top. Click on *google-chrome* to get base chrome from google. You'll see a page with a **Git Clone URL:**, copy the link that says **https://aur.archlinux.org/google-chrome.git**.

Now, open a terminal and type in the following:
```bash
git clone https://aur.archlinux.org/google-chrome.git
```
Once this is finished, you'll have a folder called *google-chrome*. CD into it
```bash
cd google-chrome
```
Now we need to build the package from the PKGBUILD file. Here, we can simply type in the following:
```bash
makepkg -s
```
What this does is resolves any dependencies you need and creates the binaries to run the program. There is more to this stage, but it's beyond the scope of this guide.

Once the process is finished, your terminal should look something like this:
```bash
[user@distro]$ makepkg -s
==> Making package: google-chrome 97.0.4692.71-1 (Wed 12 Jan 2022 06:55:57 PM EST)                                                                                                                                 
==> Checking runtime dependencies...                                                                                                                                                                               
==> Checking buildtime dependencies...                                                                                                                                                                             
==> Retrieving sources...                                                                                                                                                                                          
  -> Downloading google-chrome-stable_97.0.4692.71-1_amd64.deb...                                                                                                                                                  
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current                                                                                                                                    
                                 Dload  Upload   Total   Spent    Left  Speed                                                                                                                                      
100 85.6M  100 85.6M    0     0  9990k      0  0:00:08  0:00:08 --:--:--  9.9M                                                                                                                                     
  -> Found eula_text.html                                                                                                                                                                                          
  -> Found google-chrome-stable.sh                                                                                                                                                                                 
==> Validating source files with sha512sums...                                                                                                                                                                     
    google-chrome-stable_97.0.4692.71-1_amd64.deb ... Passed                                                                                                                                                       
    eula_text.html ... Passed                                                                                                                                                                                      
    google-chrome-stable.sh ... Passed                                                                                                                                                                             
==> Extracting sources...                                                                                                                                                                                          
  -> Extracting google-chrome-stable_97.0.4692.71-1_amd64.deb with bsdtar                                                                                                                                          
==> Entering fakeroot environment...                                                                                                                                                                               
==> Starting package()...                                                                                                                                                                                          
  -> Extracting the data.tar.xz...                                                                                                                                                                                 
  -> Moving stuff in place...                                                                                                                                                                                      
  -> Fixing Chrome desktop entry...                                                                                                                                                                                
  -> Removing Debian Cron job, duplicate product logos and menu directory...                                                                                                                                       
==> Tidying install...                                                                                                                                                                                             
  -> Removing empty directories...                                                                                                                                                                                 
  -> Removing libtool files...                                                                                                                                                                                     
  -> Purging unwanted files...                                                                                                                                                                                     
  -> Removing static library files...                                                                                                                                                                              
  -> Compressing man and info pages...                                                                                                                                                                             
==> Checking for packaging issues...                                                                                                                                                                               
==> Creating package "google-chrome"...
  -> Generating .PKGINFO file...
  -> Generating .BUILDINFO file...
  -> Adding install file...
  -> Generating .MTREE file...
  -> Compressing package...
==> Leaving fakeroot environment.
==> Finished making: google-chrome 97.0.4692.71-1 (Wed 12 Jan 2022 06:56:14 PM EST)
```
You should now have a file that ends in **.zst**, which is the file you ultimately need to finish installing google chrome. We can finish everything by running this next and final command:
```bash
sudo pacman -U *.zst
```
Yes, the final step is to let pacman resolve where everything has to go in order to run google chrome. Now just check to see if you can run chrome and you're all done!
