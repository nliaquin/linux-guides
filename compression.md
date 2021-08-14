# How to compress files and directories on Linux

If you've been using Linux for a short time, you may start asking certain questions about features you may have used on Windows that you now require on Linux. One of those features is zip compression. First, I'd like to mention that you can still zip and unzip on Linux, but there is a more effective method built into all Linux distros that has helped prevent complications called tar.gz. First we'll go over zip/unzip.


### Zip/Unzip
This program is not built into every Linux distro, so you will need to consult your package manager software to see if the program is either installed or is available in your repo list with a search.

On distros with Aptitude package manager:
> apt search zip unzip

Then to install with Aptitude:
> sudo apt install zip unzip

zip and unzip are the general names of these programs, though they may differ between different Linux distros, by the way.

On distros with Pacman:
> pacman -Ss zip unzip

Then to install with Pacman:
> sudo pacman -S zip unzip

On distros with Yum:
> yum search zip unzip

Then to install with Yum:
> sudo yum install zip unzip

Once you install or confirm that zip and unzip are installed, you can now zip and unzip files in the commandline. For example, let's say I have a directory named photos, and I need to compress them to send over an email.

While outside of the directory called photos, type
> zip -r photos photos/

To be clear here, I am using -r for recursive, meaning everything in the directory gets compressed with into the zip file. If you exclude -r, you're not compressing any files inside, and you will end up with an empty zip file. Next I wrote photos, which is what I want the name of my zip file to be, and .zip is automatically appended at the end. Next I wrote what directory I wanted to zip, which as I mentioned above, is my photos directory. I should also mention that you can specify more files and folders after photos/, like if you wanted to zip a directory named photos/ and a directory named videos/ at one time into a zip file called **myzip** as well, you can change the command to the following:
> zip -r myzip photos/ videos/

Now to unzip the myzip compressed file, you simply say:
> unzip myzip.zip

That's it!


### Tar/Gzip

The method built into every Linux distro would be tar.gz which is actually two programs combined. Gzip is for zipping individual files. It does not zip more than one file at a time, and this is because a common problem with zip files a long time ago was data loss on unzipping. When a bunch of files got zipped together, some programs would scramble the data up, and then unzipping would reveal some unexpected data loss due to the data not being restored as it was before getting zipped. Tar is for tarring zipped files together in a safe way that wouldn't scramble things up.

The best part about this is that you can both tar and gzip in one command, there is no need to call the separate programs. Using our same example above, I want to tar.gz my videos/ and photos/ directories into a compressed file called myzip.tar.gz:
> tar -zcvf myzip.tar.gz photos/ videos/

Here are what these flags are doing:
```bash
-z is to gzip the files before tarring
-c is to create new archive
-v is verbose, just means it will tell you all the files you are tarring as they are being tarred
-f specifies that we are working with a file archive
```

Okay, now let's untar and gunzip myzip.tar.gz:
> tar -zxvf myzip.tar.gz

We changed one flag, -c is now -x, which stands for extract.


And that's everything you need to know about compressing and uncompressing files/folders. Feel free to choose either method, and feel free to contact me if you have any questions or problems, nickolas@nliaquin.xyz
