# Disabling the pcspkr module in Arch
This module is what causes the infamous loud beep on Arch whenever you backspace with nothing in the shell buffer, or when auto-complete can't interpret what you're trying to type.

### Disabling temporarily
We can use the *rmmod* command to temporarily remove modules until the next boot with the following command:
```bash
rmmod pcspkr
```


### Disabling permanently
We can create a configuration file in */etc/modprobe.d/* to **blacklist** the pcspkr module.

First, we'll create a file called *nobeep.conf* with **vim** (or any other text editor of your choice) as super user:
```bash
sudo vim /etc/modprobe.d/nobeep.conf
```

Next, enter the following and save the file:
```bash
blacklist pcspkr
```

And that's it! Contact me via nickolasiaquinta@gmail.com if you have any problems or questions.
