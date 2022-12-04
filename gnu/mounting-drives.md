# Mounting Drives
## Temporarily and Permanently, and from the Terminal
Whether it's a flash drive, and external drive, or any other type of drive, it is crucial to know how to mount and unmount drives on linux. I'm going to cover seeing plugged in drives, mounting drives, unmounting drives, and even mapping drives to automatically mount on startup. This is going to be exclusively from the terminal, no GUIs.


### Table of Contents
- [Viewing Drives and Mount Space](#viewing-drives-and-mount-space)
- [Mounting Drives Temporarily](#mounting-drives-temporarily)
- [Mounting Drives Permanently](#mounting-drives-permanently)
- [NTFS Not Mounting](#ntfs-not-mounting)


### Viewing Drives and Mount Space
The very first think you should know how to do is view what drives are plugged into the computer. Open up your terminal and type the following command:
```bash
lsblk
```

If you're not very familiar with **lsblk**, or how Linux handles block devices, check out this quick start guide real quick: https://github.com/nliaquin/linux-guides/blob/main/lsblk.md

Now, assuming you ran **lsblk**, the output should roughly look like the following
```bash
[user@disto ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk 
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     4G  0 part [SWAP]
└─sda4   8:4    0   877G  0 part /home
sdb      8:11   0   1.8G  0 disk 
└─sdb1   8:12   0   1.8G  0 part /mnt/usb
```

What you should see is a list of block devices plugged in via SATA, USB Port, Serial, etc. Every block device, or drive as we'll refer to from now on, is assigned a drive letter. The first device, which will either be sda or nvme01... is the device that you're running linux off of.

```bash
[user@disto ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     4G  0 part [SWAP]
└─sda4   8:4    0   877G  0 part /home
```

Let's say you have one USB device plugged in for storage and you run **lsblk**, you will see another device called sdb.

```bash
[user@disto ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     4G  0 part [SWAP]
└─sda4   8:4    0   877G  0 part /home
sdb      8:11   0   1.8G  0 disk
└─sdb1   8:12   0   1.8G  0 part
```

Keep plugging in devices, and it'll just keep assigning devices the letters of the alphabet.

```bash
[user@disto ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     4G  0 part [SWAP]
└─sda4   8:4    0   877G  0 part /home
sdb      8:11   0   1.8G  0 disk
└─sdb1   8:12   0   1.8G  0 part
sdc      8:14   0   8.0G  0 disk
└─sdc1   8:15   0   8.0G  0 part
sdd      8:17   0   12.0G  0 disk
└─sdd1   8:18   0   12.0G  0 part
```

You might be wondering what's with the little lines leading down to those numbered drives? Those are your partitions. Every storage device has partitions, and those partitions can serve several purposes. As you can tell, sda has 4 partitions, sda1 through sda4. Since these partitions are mounted, it's pretty clear what they're for, but in case you can't tell, here is a breakdown:

```bash
[user@disto ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     4G  0 part [SWAP]
└─sda4   8:4    0   877G  0 part /home
```

Now an important think you should consider is creating a space where you're going to mount drives via USB. Think of having a directory created for every single usb port you have on your pc. I usually create these mount spaces inside of a directory provided by all Linux distros called /mnt. If you have two USB ports, consider the following:
```bash
mkdir /mnt/usb1 && mkdir /mnt/usb2
```

Now you've got mount space for either port, so when you plug in a drive, you can just mount to either of these directories.


### Mounting Drives Temporarily
Pressing forward, let's actually cover the mounting process. Let's say you've just plugged in a flash drive into a USB port. This doesn't mean it's mounted automatically, although depending on the distro or your configuration with your desktop environment software, it might. Let's assume you're running a distro that does not mount automatically.

```bash
lsblk
```

```bash
[user@disto ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     4G  0 part [SWAP]
└─sda4   8:4    0   877G  0 part /home
sdb      8:11   0   1.8G  0 disk
└─sdb1   8:12   0   1.8G  0 part
```

As you can see, sda has four different partitions with clear purposes, but in case you're new, sda1 is your boot partition where a configuration file sits for GRUB to know how to boot Linux. sda2 is your root partition where enough storage exists for global files to live in Linux. sda3 is swap storage for when you run out of RAM or go into hibernation. sda4 is where enough storage exists for all user directories and files.

Okay, so now we need to get back to your flash drive, which the block device is listed as sdb. Under sdb is sdb1, the storage partition for your flash drive. Specifically, we want to mount sdb1 because this is where the data actually exists. We do not mount sdb as a whole, this is impossible. To mount, do the following:

```bash
sudo mnt /dev/sdb1 /mnt/usb1
```

You'll be prompted for a password, and when done, you can look at the output of lsblk again:

```bash
lsblk
```

```bash
user@disto ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     4G  0 part [SWAP]
└─sda4   8:4    0   877G  0 part /home
sdb      8:11   0   1.8G  0 disk
└─sdb1   8:12   0   1.8G  0 part /mnt/rusb
```

Congratulations, your drive is mounted! You can now go inside the storage device by traveling over to **/mnt/rusb/** with the terminal or file manager of your choice.

But let's say we want to unmount it. That's easy:
```bash
sudo umount /mnt/rusb
```

And you're done!


### Mounting Drives Permanently
Now let's say you're a smart Linux user who does not store personal files on the same drive where Linux lives. You've got an SSD plugged in via SATA and you want to configure it to automount alongside Linux every boot. We are going to learn how to modify the /etc/fstab file now.

First, don't plug any flash drives in and make sure your SATA SSD is obviously set up inside your computer:

```bash
lsblk
```

```bash
user@disto ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     4G  0 part [SWAP]
└─sda4   8:4    0   877G  0 part /home
sdb      8:11   0   1.0T  0 disk
└─sdb1   8:12   0   1.0T  0 part
```

Assuming the SSD is one terabyte, we have verified that Linux acknowledges it is plugged in. Let's create a mount space for it now:

```bash
sudo mkdir /mnt/data
```

It would also be a good idea to make sure you change the owner of this directory to your user so that problems do not arise when reading or writing data in this directory:

```bash
sudo chown *username*:*username* /mnt/data/
```

We have now created a good place to map this drive in /etc/fstab. First, let's just take a look at an example of what your fstab file looks like right now:

```bash
cat /etc/fstab
```

```bash
# Static information about the filesystems.
# See fstab(5) for details.

# <file system```bash<dir```bash<type```bash<options```bash<dump```bash<pass>
# /dev/sda2
UUID=342h79d2-9293-4242-811e-dk8t26149a31       /               ext4            rw,relatime     0 1

# /dev/sda1
UUID=FB4E-R56F          /boot/efi       vfat            rw,relatime,fmask=0022,dmask=0022,codepage=437,iocharset=ascii,shortname=mixed,utf8,errors=remount-ro   0 2

# /dev/sda4
UUID=bjgk3486-0295-442c-a5d5-a057rhfj93f4       /home           ext4            rw,relatime     0 2

# /dev/sda3
UUID=i23fedb7-2daf-461c-824b-3703b6h37hjf       none            swap            defaults        0 0
```

Going off of the figure you saw in previous sections, it should be clear that fstab has mappings for all the partitions on the main drive running Linux. Before we can continue, we need to 
figure out what the UUID of your external SSD is with the following:

```bash
sudo blkid
```

```bash
/dev/sdd: UUID="b24c0ad9-aa7b-4073-9714-22c77567428f" BLOCK_SIZE="4096" TYPE="ext4"
/dev/sda4: UUID="bjgk3486-0295-442c-a5d5-a057rhfj93f4" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="d1bb5675-04"
/dev/sda2: UUID="342h79d2-9293-4242-811e-dk8t26149a31" BLOCK_SIZE="4096" TYPE="ext4" PARTUUID="d1bb5675-02"
/dev/sda3: UUID="i23fedb7-2daf-461c-824b-3703b6h37hjf" TYPE="swap" PARTUUID="d1bb5675-03"
/dev/sda1: UUID="FB4E-R56F" BLOCK_SIZE="512" TYPE="vfat" PARTUUID="d1bb5675-01"
/dev/sdb1: LABEL="data" UUID="e0f24303-8957-41f4-81b8-3c528edcc481" BLOCK_SIZE="4096" TYPE="ext4" PARTLABEL="data" PARTUUID="6a2c22dd-7703-4d7f-b269-e324d68e3dcc"
```

The UUID for sdb1 is e0f24303-8957-41f4-81b8-3c528edcc481, so copy that right now. What we want to do now is edit fstab and add sdb1 to the bottom. Use any text editor of your choice, but personally I use vim:

```bash
sudo vim /etc/fstab
```

Go to the very end of the file and hit enter a couple of times and add the following:

```bash
# /dev/sdb1     data drive
UUID=e0f24303-8957-41f4-81b8-3c528edcc481       /mnt/data       ext4            defaults        0 0
```

I had you copy the UUID down to paste it in after the part where you'll type *UUID=*. After that, you'll tab and type out the mounting area where the data will be available to you. Tab again and enter the filesystem type for this drive, in this instance it's ext4. If it's not ext4 and is instead ntfs or some other filesystem, make sure you enter the proper filesystem. Tab again and just enter *defaults*, don't worry about this option. Lastly, tab once more and enter *0 0* since you don't want to include a dump operation, nor do we want to do a fsck on the drive when it mounts.

Okay, that was a lot of info, but let's cut to the end here. Now, you're going to want to save your changes to this file and exit. After that, we should test to see if the changes made will allow for successful bootup, since a bad fstab can cause a failure to boot properly into linux. Do the following:

```bash
sudo mount -a
```

If there is no output, you've done everything correctly. If there is output, you've got a problem... Make sure to re-read the guide if you have any problems, or email me nickolasiaquinta@gmail.com


### NTFS Not Mounting
I just wanted to cover one last subject. If your flash drive or external drive uses the ntfs filesystem, you should know that ntfs is not supported on Linux by default. There is, however, some software you can download on any linux distro.

On debian-based distros:
```bash
sudo apt install ntfs-3g
```

On redhat-based distros:
```bash
sudo yum install ntfs-3g
```

On arch-based distros:
```bash
sudo pacman -S ntfs-3g
```

This resolves problems regarding compatability with ntfs on any drive.
