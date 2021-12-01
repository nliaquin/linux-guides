# Updating Arch Mirror List
You may have recently run into an issue as of 5/1/2021 where, if you followed my last guide on installing Arch Linux, you're getting errors when conducting a system-wide update. This is because the mirrors have recently changed, and now everyone is getting an ssl error from the main repositories. There are two ways to update this list.

### Method 1: Use my mirrorlist
I update my mirrorlist once a month, you can find it at https://github.com/nliaquin/arch-mirrors

To do this properly, do the following:
```bash
git clone https://github.com/nliaquin/arch-mirrors.git
```

```bash
cat arch-mirrors/mirrorlist ```bash/etc/pacman.d/mirrorlist
```

And you're done!

### Method 2: Use the mirror generator
This method is just a step longer.
- First visit https://archlinux.org/mirrorlist/
- Select your country, make sure http, https, and IPv4 are selected
- Click on Generate List

Now you'll be redirected to a new web page. Just copy all of the contents inside of the page. Either in the terminal or with a graphical text editor, modify the /etc/pacman.d/mirrorlist file. Clear all of the current contents of the file out and paste in the copied contents you got from the arch website. This process took a little longer than method 1, but it's a way for you to be independent and not rely on my mirrorlist, although I basically generate my mirrorlist the same way as method 2. The choice is yours, and as always, email me at nickolas@nliaquin.xyz for any questions or help.
