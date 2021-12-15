# Using Wifi from the Commandline
There's a few ways to connect to wifi via the commandline, and we're going to go over two of them here.

### Table of Contents
- [Choosing a method](#choosing-a-method)
- [Using iwd](#using-iwd)
- [Using wpa_supplicant](#using-wpa_supplicant)
- [Configuring dhcp](#configuring-dhcp)

### Choosing a method
The traditional method has been the wpa_supplicant route. This method has been around for a while, and is usually found on most distros by default. Our second, and easiest, method is iwd which requires less work than wpa_supplicant. Both methods are valid, but depending on the distro, iwd may not be available at all. Regardless of either method, you will always need dhclient and dhcpcd, which handle the dynamic host protocols. Before going any further, let's install both:

For Debian-based distros
```bash
sudo apt install dhcpcd dhclient
```

For RedHat-based distros
```bash
sudo yum install dhcpcd dhclient
```

For Arch-based distros
```bash
sudo pacman -S dhcpcd dhclient
```

Now just enable the service and we'll be good to go on this side
```bash
systemctl enable dhcpcd
```


### Using iwd
If you need to install iwd, the package is called iwd. Use a temporary internet source to download iwd with your package manager.

After you install iwd, you need to enable it:
```bash
systemctl enable iwd
```

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


### Using wpa_supplicant
Alright, now let's go over the harder one. It's not that difficult, it just doesn't have a cool text-based GUI like iwd does.

Start by installing wpa_supplicant with your package manager. This package, like iwd, goes by no other name.

Then you will need to create a file in /etc/wpa_supplicant/ and for this example, let's assume your network interface is wlan0, your wifi ssid is **mywifi** and your wifi password is **thewormhasturned**

```bash
sudo vim /etc/wpa_supplicant/wpa_supplicant-wlan0.conf
```

Now that you're in, type the following:
```bash
ctrl_interface=/run/wpa_supplicant
```

Keep this file opened, bring up another terminal, and run the following command:
```bash
wpa_passphrase mywifi thewormhasturned
```

This should be the output of that:
```bash
[user@distro ~]$ wpa_passphrase mywifi thewormhasturned
network={
        ssid="mywifi"
        #psk="thewormhasturned"
        psk=d849b11bfc3ed571f87582fa14d1add5611df59ddee0e644ab598c9a19c2736a
}
```

Copy all of this and paste it into the file you have open (wpa_supplicant-wlan0.conf) to get this:
```bash 
ctrl_interface=/run/wpa_supplicant

network={
        ssid="mywifi"
        #psk="thewormhasturned"
        psk=d849b11bfc3ed571f87582fa14d1add5611df59ddee0e644ab598c9a19c2736a
}
```

Lastly for setting up wpa_supplicant, enter the following command to connect:
```bash
sudo wpa_supplicant -B -i wlan0 -c /etc/wpa_supplicant/wpa_supplication-wlan0.conf
```

This works once, meaning next time you start up, you have to run it again. You can add the last command to your .bash_profile, or you can enable the service for systemd like so:
```bash
sudo systemctl enable wpa_supplicant@wlan0.service
```

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
