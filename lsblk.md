# lsblk quick start
## Understanding lsblk and it's output

This will be a supplementary guide on **lsblk** that's referenced by other guides so that I don't have to keep repeating this information.

Let's assume the following scenario: you have a computer with two hard drives attached via SATA (meaning directly to the motherboard in case you're really new) and you've just plugged in a flash drive via USB. You run the **lsblk** command in a terminal and see the following output
```bash
[user@distro ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
sda      8:0    0 931.5G  0 disk 
├─sda1   8:1    0   512M  0 part /boot/efi
├─sda2   8:2    0    50G  0 part /
├─sda3   8:3    0     8G  0 part [SWAP]
└─sda4   8:4    0   236G  0 part /home
sdb      8:16   0     1T  0 disk /mnt/data
sdc      8:48   1  57.3G  0 disk 
├─sdc1   8:49   1   3.3G  0 part 
└─sdc2   8:50   1   736K  0 part
```

You should also understand that Linux organizes your blocks devices (otherwise known as hard/solid state/flash drives) in order from the disk and partitions that contain your OS to the last thing you've plugged in. Resuming with the scenario above, you should gather from the output of lsblk that **sda** is the block device containing your OS partitions (or in other words, where your Linux distro lives). This should also be obvious since the partitions are mounted at */boot/efi*, */* or *root* as it's known as, *[SWAP]*, and */home*.

Next we see a device labeled sdb, and as mentioned above, you have two hard disks configured via SATA, so the next device on the list is always going to be the second hard drive. Lastly, we see block device **sdc**, and it has two partitions, sdc1 and sdc2. I'll spoil what's on this flash drive now, it's a liveboot for Arch Linux. The first partition is for all the programs packed into the iso, and the second is the boot partition that allows the computer to run the flash drive as if it's an operating system. Which it is, by the way.

Let's give one more scenario: you've just purchased a new laptop in the year 2022. It's not just new to you, it's new hardware in general. You've loaded a flash drive with the Gentoo live iso and are partitioning your drives to prepare your new Gentoo installation. Assuming no operating system was installed on this laptop previously and is just a laptop with no software at all, you run lsblk and see the following output
```bash
[user@distro ~]$ lsblk
NAME   MAJ:MIN RM   SIZE RO TYPE MOUNTPOINTS
nvme0n1  8:0    0 498.5G  0 disk 
sda      8:48   1  57.3G  0 disk 
├─sda1   8:49   1   3.3G  0 part gentoo
└─sda2   8:50   1   736K  0 part
```

If you're new, you're probably wondering what nvme0n1 is. This is a newer standard for data storage. If you took the time to crack open your laptop and see what's inside, you'll notice a thin, rectangular chip that is somewhat flexible. This is not your average storage formfactor, as drives in the past have always been hard boxes made of metal and plastic ranging in different weights and sizes. Bulk devices that are not typical will be classfied as their own thing seperate from the *sdX* method, where X is the order in which the drive was initialized by Linux. By the way, if you haven't already noticed, the device order is indicated alphabetically in the form of *X'th* drive.

Regardless, all devices are partitioned the same way. It's just useful to understand some of these preliminary concepts. All devices plugged in are also accessed via */dev*, ie. */dev/nvme0n1* and */dev/sda*. Partitions will be referenced the same way, ie. */dev/sda1*. This is good to know when **mounting** a partition. More on mounting in the following article I wrote: https://github.com/nliaquin/linux-guides/blob/main/mounting-drives.md

That's going to be all for this guide. I hope it's helpful in getting you started with partitioning, and be sure to check out my other guides at https://github.com/nliaquin/linux-guides
