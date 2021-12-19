# Wiping Hard Drives
## or how to wipe HDD, SSD, NVME, Flash, etc, drives (there are so many)

In this guide, we're going to go over the basics of wiping a data drive of any type. This is useful for when you plan on repurposing or selling your previously used flash drives, hard drives, etc. It's importantto know this since it's often insufficient to just repartition devices since leftover data from a previous purpose can linger, become exposed, or just outright cause problems depending on the filesystem and partitioning method used.

### Using dd to wipe a drive
For some, dd is a terrifying unix utility. Targeting the wrong drive can mean losing data, which is why it's important to make sure that you can identify your partitions and block devices carefully with **lsblk**. I'm not going to cover **lsblk** here, but I did write a quick start guide at https://github.com/nliaquin/linux-guides/blob/main/lsblk.md that explains it. Go check it out and come back here after.

Now, let's assume your flash drive is the only other device plugged into your computer, other than the hard drive with Linux on it. Given this information, your flash drive should be located at */dev/sdb*. The **dd** command primarily takes in two required arguments: the *in-file* and the *out-file*. The *in-file* is what information will be written to the destination. The *out-file* is the destination. Continuing with our scenario, we would use **dd** like so:
```bash
sudo dd if=/dev/urandom of=/dev/sdb bs=1M status=progress
```

This command will take longer to complete the bigger the device is. I also added a few extra commands I'll explain. When you run **dd**, you have to run it as super user, so that's why *sudo* was there. I meantioned the two required arguments above, *in-file* and *out-file*. Those are represented by **if=/path/to/input** and **of=/path/to/output**. This is the same when burning an iso to a flash drive, where the *in-file* would be an iso file or full path to an iso file, and *out-file* would be the flash drive inside of the */dev* directory. If you're wondering why the **if=** value was */dev/urandom*, this is a method that best scrambles all of the data on the target drive so that no previous remnants of data can be recovered. 

When wiping drives, it's always recommended to include **bs=1M**, as this represents the byte size. Setting this value to 1 means every byte is scrambled. There are other uses for the **bs** value, but we won't be going over that here. Lastly, there was the *status=progress* part of the above command. This just give you output that shows the live status of how many bytes have been written out of how many bytes there are total, and will give you an idea of how long the process is going to take. You can write this as *status="progress"* as well, as you'll see many Linux Youtubers write it, but the quotes don't matter. It works either way.

That's it for this guide, I hope it gave you a better understanding of the topic. If you have any questions, feel free to email me via nickolas@nliaquin.xyz
