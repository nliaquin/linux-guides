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

This is an example of what the output might look like for a disk that has a terabyte of capacity with a standard Linux distro installed on it. You'll notice that at the bottom, there are some partitions already. This output would roughly match the output of the **ls** command, which you can find a lesson on how that works [here](). Let's assume that we've just plugged in a 60GB flash drive with absolutely nothing on it, no partitions, nothing. We'll run *fdisk -l* once more and see the following below the /dev/sda entry:
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

Disks contain sectors, including the starting byte and ending byte. It's ultimately what determines how much storage capacity we have on a partition, and in this case, the earliest byte on the disk we can use is the 2048th byte. In most cases, fdisk is really good at determining the earliest available byte on the disk, even when there are other partitions already defined. The best thing to do now is just press enter:
```bash
First sector (2048-120127487, default 2048): 
Last sector, +/-sectors or +/-size{K,M,G,T,P} (2048-120127487, default 120127487): 
```

Each partition also has the latest byte on the disk, so given we used the earliest byte (or smallest byte) in the previous option, and we want to make the biggest partition we want for storing data on this disk, let's just also leave this blank and hit enter to make the end of this partition the latest byte (or largest byte):
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

### To be continued...
