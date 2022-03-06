# How to Archive and Compress files
In this guide, I'm going to cover archiving, compressing, de-compressing, and un-archiving files on Linux. This is the equivalent of zipping/unzipping on Windows. You may have heard of such formats such as .zip, .7z, .rar, and so on. These are all just different compression algorithms, and it's good to know the pros and cons of each one. More on this below.


## Archiving, otherwise known as tar
On Linux, the act of compression is split into two different steps: archiving and compressing. One Windows, usually these two things are done all at once, but on Unix-based operating systems, we still split the two actions as we did decades ago. The reason for this is because the archiving was useful as its own feature, and still is.

Archiving is done by using tar, which stands for tape archive, back when data was stored on tapes. Tar, tarring, tarballing, if you hear any one of these words, it all just means the same thing. Archiving is the act of taking a bunch of files and rolling them into one file. This is useful for sending multiple files via email, or copying a bunch of files to a web server.


## Compression
On Unix, you do not compress without archiving first. You can always archive without compressing, but you cannot compress without archiving. The only exception to this rule is if you are only compressing one file. Like I mentioned earlier, archiving is the act of taking a bunch of files and putting them into one file. Think of tar files as containers. Better yet, think of files as groceries and tar as the grocery bag.

Compression is simply the act of using an algorithm to make something smaller in byte size. We do this for many reasons, including storing things away on backup drives with limited space for later use, getting past file upload limits, decreasing the time it takes to upload or download something, etc.

Compression is not as cut and dry as archiving; there are many compression formats available on Linux. Each compression algorithm has pros and cons, or even tradeoffs. One of the most common compression formats is gz, which is considered the middle-ground of the compression formats. While gz does a decent job of squishing the files down, it also comes at the cost of being a **lossy** format.

Lossy means that you have lost some amount of bytes in the process of archiving. This will lower the quality of the files inside, thus creating possible complications for when you de-compress. This is the ultimate tradoff, the smaller a compression algorithm can make something, the more **lossy** those files become. Imagine you take something and compress it with gz. Now de-compress those files. Now take those same files you just de-compressed and compress them again. You are are now loosing double the bytes you lost last time you compressed.

Of course, in practice, you're not goint to do that. You are going to compress an archive one time, and you are going to keep that compressed file. When you want to decompress, you should still keep a copy of the compressed file for later. Think of it as making decompressed copies of the compressed file from now on. Now in contrast, there are **lossless** compression algorithms, meaning you lose nothing when you compress/decompress. It is probably best that you stick to **lossless** compression, as this is more ideal for long-term storage.

There are more compression algorithms available, some widely used, others not. If you want my opinion, lz4 is your best bet for great compression at no loss, but you need to remember that lz4 does not come standard on all Unix systems. If you plan on de-compressing on a web server that runs cpanel, you might have a hard time unless you know how to build and compile it from source. You also have to count on gcc being available on cpanel at that point. The next best compression is xz as it is available on all Linux distros by default now, but a lot of criticism has come out about it's long-term compression viability. You can make the decision for yourself, but I will be teaching xz and lz4 below.


## How to archive files
Let's go over archiving and unarchiving here. Remember, when archiving, there is no point to only targeting one file. You should always put a collection of files into a folder, for convenience, and then archive the folder using **tar**.

Let's say I have the following files:
```bash
[user@distro]$ ls
font1.ttf  font2.ttf  image1.png  image2.png  video1.mp4  video2.webm
```

Let's start by creating a new folder named *project*
```bash
mkdir project
```

Now let's move all those files into *project*
```bash
mv *.* project/
```

This was just a quick way to get all those files into a folder, representing all files with an extension in the current directory. Do not do this in a directory with a bunch of files that you do not want to archive.

Now you should just see this when you list directories again:
```bash
[user@distro]$ ls
project
```

To archive, do the following:
```bash
tar cfv project.tar project/
```
Which should produce the following:
```bash
[user@distro]$ tar cfv project.tar project/
project/
project/image1.png
project/video1.mp4
project/video2.webm
project/font2.ttf
project/image2.png
project/font1.ttf

[user@distro]$ ls
project  project.tar
```
A quick note on what *tar cfv* is doing: The cfv are all separate arguments. The c and f were crucial to making the archive, c meaning create, f meaning we're archiving files/directories with files and not some other format like tapes. The v argument was just so we can see things as they're being archived verbosely. This is not a crucial argument.

Now you have a tar archive. You can see what is inside a tar archive with the following:
```bash
tar -tf project.tar
```
Which should produce the following:
```bash
[user@distro]$ tar -tf project.tar
project/
project/image1.png
project/video1.mp4
project/video2.webm
project/font2.ttf
project/image2.png
project/font1.ttf
```

There are many arguments available for tar, so be sure to check out the **man page**:
```bash
man tar
```


## How to compress files/archives
In practice, in order to use these compression formats, you need to use programs. Obviously, you might be thinking to yourself, but I mention this because on Linux, compression and de-compression are separated into two different programs. If we want to create a gz, we use **gzip**. If we want to de-compress a gz, we use **gunzip**. Those program names seem clever, right? Well, some programs don't separate the two, and instead just use the same command with different arguments, like xz does.

### xz
Let's go back to our scenario with project.tar and compress it using xzip. To start, be sure xz is installed. It's just called **xz** in all package managers.

Debian:
```bash
sudo apt install xz
```

Fedora:
```bash
sudo yum install xz
```

Arch:
```bash
sudo pacman -S xz
```

To simply xzip project.tar, we do the following:
```bash
xz -z project.tar
```
Which produces the following:
```bash
[user@distro]$ xz -z project.tar
[user@distro]$ ls
project  project.tar.xz
```
Notice you lost your tar file and now you have a tar.xz file instead. This is the easiest way to compress our tar file with xz. It leaves all options defaulted, including the amount of compression which is left at level 6. If you wanted your xz file to be smaller, you can override the compression level with an additional flag like so:
```bash
xz -z -9 project.tar
```
It will be slower, but the file will be smaller.

Now that you have project.tar.xz, let's say you're ready to de-compress it:
```bash
xz -d project.tar.xz
```
You will now see you tar file come back, but your xz file will be gone. You can keep your original xz file while also de-compressing by adding the -k flag:
```bash
xz -k -d project.tar.xz
```
Just remember to put the -k before -d, otherwise this won't work.

You can also see the contents of the tar file inside using **tar -tf** on project.tar.xz:
```bash
tar -tf project.tar.xz
```
Which produces the following:
```bash
[user@distro]$ tar -tf project.tar.xz
project/
project/image1.png
project/video1.mp4
project/video2.webm
project/font2.ttf
project/image2.png
project/font1.ttf
```

To simply view the contents of the xz file, in case there is more than one tar inside, use **xz -l**:
```bash
xz -l project.tar.xz
```

Now let's go over a proprietary form of compression. Eventually, you will come across .zip files from Windows, and you will need to know how to handle these. You may even need to send a Windows user a zip.

### Zip/Unzip
This program is not built into every Linux distro, so you will need to consult your package manager software to see if the program is either installed or is available in your repo list with a search.

On distros with Aptitude package manager:
```bash
apt search zip unzip
```

Then to install with Aptitude:
```bash
sudo apt install zip unzip
```

zip and unzip are the general names of these programs, though they may differ between different Linux distros, by the way.

On distros with Pacman:
```bash
pacman -Ss zip unzip
```

Then to install with Pacman:
```bash
sudo pacman -S zip unzip
```

On distros with Yum:
```bash
yum search zip unzip
```

Then to install with Yum:
```bash
sudo yum install zip unzip
```

Once you install or confirm that zip and unzip are installed, you can now zip and unzip files in the commandline. For example, let's say I have a directory named photos, and I need to compress them to send over an email.

While outside of the directory called photos, type
```bash
zip -r photos photos/
```

To be clear here, I am using -r for recursive, meaning everything in the directory gets compressed with into the zip file. If you exclude -r, you're not compressing any files inside, and you will end up with an empty zip file. Next I wrote photos, which is what I want the name of my zip file to be, and .zip is automatically appended at the end. Next I wrote what directory I wanted to zip, which as I mentioned above, is my photos directory. I should also mention that you can specify more files and folders after photos/, like if you wanted to zip a directory named photos/ and a directory named videos/ at one time into a zip file called **myzip** as well, you can change the command to the following:
```bash
zip -r myzip photos/ videos/
```

Now to unzip the myzip compressed file, you simply say:
```bash
unzip myzip.zip
```

Let's go over the other very popular proprietary compression format.


### 7z

7zip is not the most popular compression method in the world, but it's out there. Unfortunately, the previous method mentioned will not work on 7z files. On most distros, the program provided by the default package manager is p7zip. This program will both compress and uncompress 7z files on its own.

To extract everything in the proper format it was originally compressed in (with all file and folder structures in tact):
```bash
7z x *example.7z*
```

To archive a file/folder:
```bash
7z a *archive name* *file/folder to archive*
```

To see the contents of a 7z file:
```bash
7z l *example.7z*
```

There's not much else to p7zip, but I recommend not using this form of compression if you are trying to archive (fully preserve) everything with the original metadata tied to it (such as users, dates, permissions, etc).


## Combining tar and xz
I wait to tell people how to do this because it's important to learn how to archive and compress separately before learning this trick. The tar program has flags for compressing xz, gz, etc, right after the archiving process in one command. Let's use our project directory example again

To tar and xz together:
```bash
tar cfvJ project.tar.xz project
```
The arguments are as follows: **c**reate archive, **f**iles are the type, **v**erbose to show us each file we're working on, and **J** for xz. Yeah, that last part is odd, why a capital J? Well, for each form of compression directly supported by the tar program, a letter that wasn't in use for another argument was assigned. A lowercase z was given to gz, which you'll see in the section below.

Lastly for this section, let's de-compress and un-archive in one shot:
```bash
tar xvfJ project.tar.xz
```
This will also keep the tar.xz file.

### Combining Tar/Gzip
This is an old part of the guide before I revamped it. If I repeated myself here, my apologies. If you understood the concepts above, this section is an option one.

The best part about this is that you can both tar and gzip in one command, there is no need to call the separate programs. Using our same example above, I want to tar.gz my videos/ and photos/ directories into a compressed file called myzip.tar.gz:
```bash
tar -zcvf myzip.tar.gz photos/ videos/
```

Here are what these flags are doing:
```bash
-z is to gzip the files before tarring
-c is to create new archive
-v is verbose, just means it will tell you all the files you are tarring as they are being tarred
-f specifies that we are working with a file archive
```

Now if you don't know what is inside of a tar.gz, you can preview the contents with the following:
```bash
tar -tvf myzip.tar.gz
```

Okay, now let's untar and gunzip myzip.tar.gz:
```bash
tar -zxvf myzip.tar.gz
```

We changed one flag, -c is now -x, which stands for extract.


And that's everything you need to know about compressing and uncompressing files/folders. Feel free to choose either method, and feel free to contact me if you have any questions or problems, nickolas@nliaquin.xyz
