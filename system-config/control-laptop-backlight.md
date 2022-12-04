# Controlling Laptop Backlight in Linux
Many people have struggled with this, but fortunately it's easier than it seems. This method will not require sudo or root privileges to run the program.

### Installation
The program we're obtaining is brightnessctl

For Debian-based distros:
```bash
sudo apt install brightnessctl
```

For RedHat-based distros:
```bash
sudo yum install brightnessctl
```

For Arch-based distros:
```bash
sudo pacman -S brightnessctl
```

I do not believe this program requires xorg, but I am personally using it on xorg, just a heads up.


### Using brightnessctl
You can set the brightness a few different ways.

If you want to set the specific brightness percentage:
```bash
brightnessctl set 10%
```

You can increment the brightness:
```bash
brightnessctl set 10%+
```

You can decrement the same way:
```bash
brightnessctl set 10%-
```
