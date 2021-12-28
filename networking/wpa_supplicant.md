# wpa_supplicant
### wifi from the commandline

Just a heads up, this is not the most permanent solution to wifi from the commandline. You can make this work in a sort of duct-taped, boot-strapped way, but this method is ideally for when you need internet during the installation of a Linux distro or a temporary connection.


### Installing the necessary packages
For Debian-based distros
```bash
sudo apt install dhcpcd dhclient wpa_supplicant
```

For RedHat-based distros
```bash
sudo yum install dhcpcd dhclient wpa_supplicant
```

For Arch-based distros
```bash
sudo pacman -S dhcpcd dhclient wpa_supplicant
```

I'd like to mention, wpa_supplicant, and likely dhclient with dhcpcd, are already installed on most distros by default.

Now just enable the service and we'll be good to go on this side
```bash
systemctl enable dhcpcd
```


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

This works once, meaning next time you start up, you have to run it again.


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
