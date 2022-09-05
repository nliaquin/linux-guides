# iwd
### wifi from the commandline

Lately, iwd is being spotted as the go-to wifi tool for many distros, and it's a fairly straightforward program to use. NetworkManager is sort of being phased out over time, but is not going away just yet, so you should still learn nmcli, but we're going to go over this nice little alternative here.


### Installing necessary packages
For Debian-based distros
```bash
sudo apt install dhcpcd dhclient iwd
```

For RedHat-based distros
```bash
sudo yum install dhcpcd dhclient iwd
```

For Arch-based distros
```bash
sudo pacman -S dhcpcd dhclient iwd
```

Now just enable the services and we'll be good to go on this side
```bash
systemctl enable dhcpcd
systemctl enable iwd
```


### Using iwd
First you need to figure out your network device. Your wireless network devices are queried and will always be refered to as wlan with a number after. If you have two wireless adapters, the first will be wlan0 and the second wlan1.

Confirm your device by starting iwd:
```bash
iwctl
```

Then list your network devices:
```bash
station list
```

If this comes up with nothing, exit iwd and enter the following command:
```bash
sudo rfkill unblock wifi
```

Now go back into iwd:
```bash
iwctl
```

List all network interfaces with the following command:
```bash
station list
```

You should now see wlan0 if you have at least one wireless adapter. If not, look into whether your laptop or adapter are compatible with Linux in the first place. Many Toshiba modules have this problem for instance. For the remainder of this guide, I'm going to assume you have wlan0.

Scan for nearby wifi:
```bash
station wlan0 scan
```

List wifi networks:
```bash
station wlan0 get-networks
```

Connect to a wifi network:
```bash
station wlan0 connect **network name**
```

If there is a password, it'll ask for it. Type it and hit enter. No confirmation will be given upon successful connection, but failed connection will give a warning.


### Configuring dhcp
This is the most important part, as wifi will not fully work without it. Run the following:
```bash
ip link set wlan0 up
```

And lastly, set up dhclient on wlan0:
```bash
dhclient wlan0
```

That's it. Now just ping a website a couple times:
```bash
ping -c2 google.com
```

If it worked, you'll successfully send icmp requests and get feedback from google.com.

If it didn't... well, you'll either have to repeat a couple of steps in this guide or email me at nickolas@nliaquin.xyz for help. I'll be glad to assist anyone in need. This all can get very complicated, but it's worth learning if you are working towards becoming a system administrator or plan on configuring and interfacing with a home server via ssh. Thanks for using my guide.
