# Installing and connecting to wifi with IWD
### Updated September 5th, 2022

IWD is an alternative to NetworkManager on many distros and is a really simple networking software which aims to simplify connecting to wifi compared to nmcli. IWD is still buggy and not 100% reliable for many, so make sure to still learn NetworkManager/nmcli.

### Installing necessary packages
First, let's install the necessary software:
```bash
sudo pacman -S dhclient iwd
```

Enable IWD through systemd:
```bash
systemctl enable iwd
```

Lastly, make sure you do not have dhclient enabled through systemd:
```bash
systemctl disable --now dhcpcd
```
IWD is no longer compatible with dhcpcd, you will lose your networking interface every time you restart.

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

And lastly, invoke dhclient on wlan0:
```bash
dhclient wlan0
```
A note on dhclient, you now have to invoke this on restart every time as of 9/5/2022. It used to be you could just enable dhcpcd and let systemd run that on startup every time, but now dhcpcd and IWD don't get along together. This is why it's probably better to just learn NetworkManager.

That's it. Now just ping a website a couple times:
```bash
ping -c2 google.com
```

If it worked, you'll successfully send icmp requests and get feedback from google.com.
