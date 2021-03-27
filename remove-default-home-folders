# Remove default home folders

## Preface
On most Linux distros, you'll notice each user gets a sat of default folders in their home directory. This is because sometime during the early days of Linux' development, some developers had it in mind that they might want to better appeal to non-Linux users to come over by adding more conviences like Windows does. One of those conveniences was building default Videos, Pictures, Music, and other sym-linkable directories with specific organizational and viewing configurations built in by default.

For some of us who are strict about our file and directory naming conventions, this is an incocnvience. As someone who mostly lives at the terminal, I personally prefer lowercase directory and filenames. The code that handles setting up default user home directories does not allow you to change these directory names, at least not permanently, and removing them is not permanent either. There is a routine called on login that will check to see if the directories it expects to be in your home folder are there. If not, it recreates them, and you will now either have lowercase and uppercase copies of the same folders by name, not by content, or it will just rename your lowercase folders back to uppercase. It really depends on the distro at times, some maintainers will change the code to fit their standard. Some maintainers just don't put it in at all.

Now I need to answer the question that should be on your mind right now, what is the source of these default directories? Well I said the distro maintainer has a say in some behavior, but they're not the source of this. The answer is, like with all other things in Linux, a program. Which program? xdg-user-dirs. This program, this free software, is included with the majority of Linux distros. It was made a long time ago, and it's almost been as standard as the GNU coreutils at this point. I've only ever found myself using two distros that didn't include this program.

## Removing xdg-user-dirs
The hard part of reading all that preface and story is out of the way, now let's talk removing it. This program is split into several modules in the bin folder. Last I checked, all package managers view this program the same way.

For pacman package manager:
> pacman -R xdg-user-dirs*

For aptitude package manager:
> apt --purge remove xdg-user-dirs*

For yum package manager:
> yum remove xdg-user-dirs*

## Epilogue
You might be asking "Why use the purge flag with apt?" Well, for whatever reason, I've found that not getting rid of the package and its files completely on certain distros allows it to come back on its own. I suspect this is after a system upgrade. 

In conclusion, you are now mostly in control of your home directories. I say mostly because not all programs are created to standard. It's a pain at times, but many programs do not follow any Linux standards, typically programs not written with Linux in mind at first. Sometimes this is also due to newer developers just starting out in the Linux free software space. Most web browsers automatically create a big Downloads folder in your home directory, regardless if you told it to ask where to download every time. With the trends in home computing being established long over the recent two decades, it's hard to escape the clutter.
