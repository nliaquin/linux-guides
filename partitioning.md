# Partitioning
### with fdisk (and formatting with mkfs)

There are many tools out there both graphical and commandline interface that can partition disks, but in this guide, I want to cover the basics of how to use **fdisk**, the partitioning tool that comes packed with every distro since the '90s. If you have a basic understanding of **fdisk**, you'll have more than enough of an understanding to use other tools such as **cfdisk**, **parted**, etc. This is because fdisk was the fundamental software that was spun off of by all these other tools. As a bonus, I'm going to cover formatting very briefly at the end with the **mkfs** utility.

### Listing Disks
When using fdisk, run as root/superuser. The first thing we want to do is list our disks with the following command:
```bash
fdisk -l
```

The output should look a little something like this, depending on your setup:
```bash
Disk /dev/sda: 931.51 GiB, 1000204886016 bytes, 1953525168 sectors
Disk model: Samsung SSD 860 
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disklabel type: dos
Disk identifier: 0xd1bb5675

Device     Boot     Start        End    Sectors  Size Id Type
/dev/sda1            2048    1050623    1048576  512M 83 Linux
/dev/sda2         1050624  105908223  104857600   50G 83 Linux
/dev/sda3       105908224  122685439   16777216    8G 83 Linux
/dev/sda4       122685440 1953525167 1830839728  873G 83 Linux
```

This is an example of what the output might look like for a disk that has a terabyte of capacity with a standard Linux distro installed on it. You'll notice that at the bottom, there are some partitions already. This output would roughly match the output of the **lsblk** command, which you can find a lesson on how that works [here](https://github.com/nliaquin/linux-guides/blob/main/lsblk.md). Let's assume that we've just plugged in a 60GB flash drive with absolutely nothing on it, no partitions, nothing. We'll run **fdisk -l** once more and see the following below the /dev/sda entry:
```bash
Disk /dev/sdc: 57.28 GiB, 61505273856 bytes, 120127488 sectors
Disk model: Ultra           
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

### Partitioning
Now that we have a device to partition in mind, the 60GB flash drive as identified as */dev/sdc* (indicated by the first line where it says *Disk /dev/sdc*), let's start by running **fdisk** with */dev/sdc* as the argument:
```bash
sudo fdisk /dev/sdc
```

You're going to see the following output:
```bash
Welcome to fdisk (util-linux 2.37.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table.
Created a new DOS disklabel with disk identifier 0xad8d8859.

Command (m for help): 
```

This is the standard cli for fdisk. It might be in your best interest to enter *m* just to see what your options are, but for now, let's create a new partition **table** by entering *o*:
```bash
Command (m for help): o
Created a new DOS disklabel with disk identifier 0x0b55f38d.
```

Partition tables are necessary to define the partitions, and it's important to select the right kind of partition table as to create the right kind of partitions. In most storage solutions, we use DOS partition tables. Now with that out of the way, let's enter *n* for **new partition**:
```bash
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): 
```

For this scenario, let's just say we're creating one standard storage partition, which warrants us to enter *p* for **primary** (or just leave it blank since that's the default):
```bash
Select (default p): p
Partition number (1-4, default 1): 
```

Now just leave this as default (1) since we have no other partitions and hit enter:
```bash
Partition number (1-4, default 1): 1
First sector (2048-120127487, default 2048): 
```

Disks contain sectors, including the starting sector and ending sector. It's ultimately what determines how much storage capacity we have on a partition, and in this case, the earliest sector on the disk we can use is the 2048th sector. In most cases, fdisk is really good at determining the earliest available sector on the disk, even when there are other partitions already defined. The best thing to do now is just press enter:
```bash
First sector (2048-120127487, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-120127487, default 120127487): 
```

Each partition also has the latest sector on the disk, so given we used the earliest sector in the previous option, and we want to make the biggest partition we want for storing data on this disk, let's just also leave this blank and hit enter to make the end of this partition the latest sector:
```bash
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-120127487, default 120127487): 

Created a new partition 1 of type 'Linux' and of size 57.3 GiB.

Command (m for help): 
```

After that, fdisk takes you back to the standard starting point of the cli. Now that you've done everything above, the partition is ready to be written, but there is one more command to run in order to write the changes you've made by entering *w* for **write**:
```bash
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.
```

This also exits the program once changes are written. The reason your previous actions meant nothing until you enter *w* is just for safety, in case you realized you wrote the wrong information or were about to format the wrong disk.

### Formatting
Formatting is basically turning the partition into a usable file system, and on Linux, we accomplish this via **mkfs**, a utility that also comes standard with every Linux distro since the '90s.

You have to know what filesystems are available on your distro before you can use mkfs, although all distros come with ext3, ext4, and a few others these days. List your available filesystems with the following:
```bash
cat /proc/filesystems
```

Again, it depends on your distro or what filesystems you have installed, but the output should look a little something like the following:
```bash
nodev   sysfs
nodev   tmpfs
nodev   bdev
nodev   proc
nodev   cgroup
nodev   cgroup2
nodev   cpuset
nodev   devtmpfs
nodev   binfmt_misc
nodev   configfs
nodev   debugfs
nodev   tracefs
nodev   securityfs
nodev   sockfs
nodev   bpf
nodev   pipefs
nodev   ramfs
nodev   hugetlbfs
nodev   devpts
nodev   autofs
nodev   efivarfs
nodev   mqueue
nodev   pstore
        ext3
        ext2
        ext4
```

On Linux, I like to use ext4. Before we can format, let's use **lsblk** to see :
```bash
[user@distro ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk 
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     8G  0 part [SWAP]
└─sda4   8:4    0   873G  0 part /home
sdc      8:32   1  57.3G  0 disk 
└─sdc1   8:33   1  57.3G  0 part
```

We can confirm that the disk we modified was /dev/sdc, and the partition we created was /dev/sdc1. Therefore, to make this partition usable, we enter the following:
```bash
[user@distro ~]$ sudo mkfs.ext4 /dev/sdc1
mke2fs 1.46.4 (18-Aug-2021)
Creating filesystem with 15015680 4k blocks and 3760128 inodes
Filesystem UUID: e7fb4a51-0781-446e-995e-5c2831fc0131
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
        4096000, 7962624, 11239424

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (65536 blocks): done
Writing superblocks and filesystem accounting information: done
```

And that's it for making a basic storage device. Next, let's do something a little more serious. We're going to format a disk to install Linux onto...

### Paritioning for Linux
There are many ways to partition a drive for Linux, and here I am going to go over the standard way that will work for any distro. Let's say I have a 500GB hard drive, and I want to create the following partitions:
 - boot
 - root
 - swap
 - home

We're going to assume I have three hard drives attached to my machine via SATA, the third disk being the one I want to partition. I run **lsblk** and get the following:
```bash
[user@distro]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk 
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     8G  0 part [SWAP]
└─sda4   8:4    0   873G  0 part /home
sdb      8:16   0   3.6T  0 disk /mnt/exdd
sdc      8:32   0 465.8G  0 disk
```
My target disk is going to be **sdc**. As you can see, it's not exactly 500GB despite the label on the front, but we can at least confirm this is my disk that I want to partition.

To begin, we need to run the following command:
```bash
sudo fdisk /etc/sdc
```
We should now see the following:
```bash
[user@distro]$ sudo fdisk /dev/sdc

Welcome to fdisk (util-linux 2.37.2).
Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.


Command (m for help): 
```
Since we're starting from scratch with this drive, let's enter **o** to create a new partition table:
```bash
Command (m for help): o
Created a new DOS disklabel with disk identifier 0xeb431b83.

Command (m for help): 
```
Now let's enter **n** for new partition, and leave the partition type prompt blank to use the default option. Also, side note, all of these partitions are going to be primary partitions.
```bash
Command (m for help): n
Partition type
   p   primary (0 primary, 0 extended, 4 free)
   e   extended (container for logical partitions)
Select (default p): 

Using default response p.
Partition number (1-4, default 1): 
```
Leave this option, as well as the rest when asked which partition number, for the rest of the scenario
```bash
Partition number (1-4, default 1): 
First sector (2048-976773167, default 2048): 
```
Leave this option blank, as we want to start at the next freely available sector. Also, please keep this option default for the rest of the scenario.
```bash
Partition number (1-4, default 1): 
First sector (2048-976773167, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-976773167, default 976773167):
```
This is where we finally need to enter something. I mentioned earlier which partitions we need, and the first partition we'll make is going to be a **boot** partition. Boot partitions generally range around 512MB, so for this we enter +512M which calculates the end sector of the partition for you in fdisk.
```bash
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-976773167, default 976773167): +512M

Created a new partition 1 of type 'Linux' and of size 512 MiB.

Command (m for help):
```

Now let's establish **root**, **swap**, and **home** partitions much faster now that we understand the process better:

First we'll make our **root** partition, which needs to be at least 50G in my opinion, but it really depends on what you plan on doing with root.
```bash
Command (m for help): n
Partition type
   p   primary (1 primary, 0 extended, 3 free)
   e   extended (container for logical partitions)
Select (default p): 

Using default response p.
Partition number (2-4, default 2): 
First sector (1050624-976773167, default 1050624): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (1050624-976773167, default 976773167): +50G

Created a new partition 2 of type 'Linux' and of size 50 GiB.
```

For your **swap**, you should have at least the same amount as you have *RAM*. Many people debate over this, saying you need half as much, twice as much, etc. The truth is, you need swap space for when you hibernate and when you run out of RAM, but mostly just for hibernation. The truth is, if you have 16GB of RAM and plan on using hibernation, 16GB of swap is enough. If you have anything less than 8GB of RAM, then I would suggest doubling up on the swap, but swap is hardly used as a means of preventing running out of RAM these days. For the rest of this scenario, let's assume I have 16GB of RAM:
```bash
Command (m for help): n
Partition type
   p   primary (2 primary, 0 extended, 2 free)
   e   extended (container for logical partitions)
Select (default p): 

Using default response p.
Partition number (3,4, default 3): 
First sector (105908224-976773167, default 105908224): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (105908224-976773167, default 976773167): +16G

Created a new partition 3 of type 'Linux' and of size 16 GiB.
```

Lastly, we'll make the **home** partition. Just use the remainder of the disk for this one:
```bash
Command (m for help): n
Partition type
   p   primary (3 primary, 0 extended, 1 free)
   e   extended (container for logical partitions)
Select (default e): p

Selected partition 4
First sector (139462656-976773167, default 139462656): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (139462656-976773167, default 976773167): 

Created a new partition 4 of type 'Linux' and of size 399.3 GiB.
```
Make note, it depends on your disk, but once I got to the 4th partition, the parition type would default to *extended*. Manually enter **p** for *primary* if fdisk tries to do this.

Now we just need to write everything to disk:
```bash
Command (m for help): w
The partition table has been altered.
Calling ioctl() to re-read partition table.
Syncing disks.

[user@distro]$ 
```

Let's see how everything came out with **lsblk**:
```bash
user@distro]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk 
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     8G  0 part [SWAP]
└─sda4   8:4    0   873G  0 part /home
sdb      8:16   0   3.6T  0 disk /mnt/exdd
sdc      8:32   0 465.8G  0 disk 
├─sdc1   8:33   0   512M  0 part 
├─sdc2   8:34   0    50G  0 part 
├─sdc3   8:35   0    16G  0 part 
└─sdc4   8:36   0 399.3G  0 part
```
Disk **sdc** now has four partitions, and all that's left is to add the filesystems to them. For the **boot** partition, we should use FAT32:
```bash
[user@distro]$ sudo mkfs.fat -F32 /dev/sdc1
mkfs.fat 4.2 (2021-01-31)
```
Next, we need to use **mkswap** on the **swap** partition:
```bash
[user@distro]$ sudo mkswap /dev/sdc2 
Setting up swapspace version 1, size = 50 GiB (53687087104 bytes)
no label, UUID=7e21ghd9-a7tr-451d-91a1-dp9ad6c45l27
```
The last two partitions are up to you, you can choose any Linux filesystem. I choose ext4:
```bash
[user@distro]$ sudo mkfs.ext4 /dev/sdc3
mke2fs 1.46.4 (18-Aug-2021)
/dev/sdc3 contains a ext4 file system
        created on Sat Jan  1 20:39:38 2022
Proceed anyway? (y,N) y
Creating filesystem with 4194304 4k blocks and 1048576 inodes
Filesystem UUID: 2b3efef5-fb2d-4704-b7bb-6d26e2e1e7c4
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
        4096000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (32768 blocks): done
Writing superblocks and filesystem accounting information: done   

[user@distro]$ sudo mkfs.ext4 /dev/sdc4
mke2fs 1.46.4 (18-Aug-2021)
/dev/sdc4 contains `DOS/MBR boot sector' data
Proceed anyway? (y,N) y
Creating filesystem with 104663814 4k blocks and 26173440 inodes
Filesystem UUID: 79c006ac-f8f2-4dad-9754-27a2133663e9
Superblock backups stored on blocks: 
        32768, 98304, 163840, 229376, 294912, 819200, 884736, 1605632, 2654208, 
        4096000, 7962624, 11239424, 20480000, 23887872, 71663616, 78675968, 
        102400000

Allocating group tables: done                            
Writing inode tables: done                            
Creating journal (262144 blocks): done
Writing superblocks and filesystem accounting information: done
```
Make note, I have to tell it that I would like to overwrite the last two boot sectors. This is usually a sign that the disk has been used before. Be weary of this when buying second-hand drives.

Congratulations, you're now ready to install Linux on this hard drive.
