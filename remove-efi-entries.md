# Removing Old UEFI Boot Entries
### using efibootmgr

If you've ever installed multiple operating systems or linux distros on your computer, you may notice your BIOS boot menu has saved all of these entries, whether those operating systems are still installed. In this guide, we're going over how to remove these entires using **efibootmgr**.

### Installing efibootmgr
On Debian-based distros:
```bash
sudo apt install efibootmgr
```

On RedHat-based distros:
```bash
sudo yum install efibootmgr
```

On Arch-based distros:
```bash
sudo pacman -S efibootmgr
```


### Removing EFI boot entries
First we need to actually list the available entries in the EFI partition:
```bash
efibootmgr
```
Yes, simply running the program without arguments will list your entries. Let's assume you used to have Debian installed, and now you use Arch. The output of **efibootmgr** might look like this:
```bash
BootCurrent: 0000
Timeout: 1 seconds
BootOrder: 0000,0001,0002,0003,0004
Boot0000* arch
Boot0001* debian
Boot0002* Hard Drive 
Boot0003* UEFI:CD/DVD Drive
Boot0004* UEFI:Removable Device
Boot0005* UEFI:Network Device
```

To remove *debian* from the list of entries, run the following command:
```bash
sudo efibootmgr -b 0001 -B
```
Now if you run **efibootmgr** again, the output would come out like this:
```bash
BootCurrent: 0000
Timeout: 1 seconds
BootOrder: 0000,0001,0002,0003,0004
Boot0000* arch
Boot0002* Hard Drive 
Boot0003* UEFI:CD/DVD Drive
Boot0004* UEFI:Removable Device
Boot0005* UEFI:Network Device
```


### Removing GRUB entries
Now, you may have a debian entry in the GRUB bootloader still, but we can also remove that simply by running the following:
```bash
sudo update-grub
```
You could use **grub-mkconfig**, but that could exclude some entries if you still maintain more than one distro using dualboot.

That's it for this guide, please feel free to contact me via nickolas@nliaquin.xyz for any problems or questions.
