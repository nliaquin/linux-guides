# Installing and connecting to wifi with NetworkManager
### Created September 5th, 2022

NetworkManager has been the defacto standard application for connecting Linux machines to the internet for years, and while competitors such as IWD have come along, there are still some bugs and hardware constraints that IWD is working out. I'll be going over how to install NetworkManager, and how to use nmcli to connect to wifi.

### Installation
We're going to need NetworkManager, wpa_supplicant, and wireless_tools to start off:
```bash
sudo pacman -S wpa_supplicant wireless_tools networkmanager
```

If you have dhcpcd enabled with systemd, disable it now, it will interfere with NetworkManager:
```bash
sudo systemctl disable --now dhcpcd
```

Next, enable NetworkManager and wpa_supplicant:
```
sudo systemctl enable --now NetworkManager.service
sudo systemctl enable --now wpa_supplicant.service
```

You might need to restart at this point.


### Connecting to wifi
To see which networks are available, we're going to invoke nmcli (NetworkManager command-line interface) and specify that we want to use our wifi adapter device using the term 'wifi', combined with the final command 'list':
```bash
nmcli device wifi list
```
If you have one wireless adapter, NetworkManager will already assume to use it when invoking 'wifi'.

You should now see a list of networks near your device like so:
```bash
[user@host ~]$ nmcli device wifi list
IN-USE  BSSID              SSID                             MODE   CHAN  RATE        SIGNAL  BARS  SECURITY
        80:D0:4A:86:10:64  --                               Infra  6     130 Mbit/s  82      ▂▄▆█  WPA1 WPA2 802.1X
        22:EF:BD:82:5B:51  --                               Infra  6     130 Mbit/s  79      ▂▄▆_  WPA2
        80:D0:4A:86:10:6B  XFINITY                          Infra  157   540 Mbit/s  75      ▂▄▆_  WPA2 802.1X
        80:D0:4A:86:10:62  --                               Infra  6     130 Mbit/s  72      ▂▄▆_  WPA2
        12:59:32:C4:9B:05  --                               Infra  6     65 Mbit/s   54      ▂▄__  WPA2
        B0:0C:D1:06:B9:42  DIRECT-41-HP OfficeJet Pro 6970  Infra  6     65 Mbit/s   47      ▂▄__  WPA2
        12:59:32:63:8B:FB  --                               Infra  6     65 Mbit/s   40      ▂▄__  WPA2
```

The problem you might have is that you might not see you network listed. This can be for a number of reasons, but in my case, it was because my network is hidden. In any case, you can still connect to your network, hidden intentionally or not, so long as you know the name of your network and the password.

Let's assume your network is named *john-wifi* and the password is *apple1928*. Here is how you would connect:
```bash
nmcli device wifi connect john-wifi password apple1928 hidden yes
```
You should see the following output if this connection was successful:
```bash
[user@host ~]$ nmcli device wifi connect john-wifi password apple1928 hidden yes
Device 'wlan0' successfully activated with '657b902c-0913-44ee-b322-c59ef507af4c'.
```

Now if you run the list command again like above, you should see the following:
```bash
[user@host ~]$ nmcli device wifi list
IN-USE  BSSID              SSID                             MODE   CHAN  RATE        SIGNAL  BARS  SECURITY
*       80:D0:4A:86:10:7A  john-wifi                        Infra  6     130 Mbit/s  82      ▂▄▆█  WPA2
        80:D0:4A:86:10:6B  XFINITY                          Infra  157   540 Mbit/s  74      ▂▄▆_  WPA2 802.1X
        80:D0:4A:86:10:6C  --                               Infra  157   540 Mbit/s  74      ▂▄▆_  WPA1 WPA2 802.1X
        80:D0:4A:86:10:69  xfinitywifi                      Infra  157   540 Mbit/s  74      ▂▄▆_  --
        80:D0:4A:86:10:6A  --                               Infra  157   540 Mbit/s  65      ▂▄▆_  WPA2
        B0:0C:D1:06:B9:42  DIRECT-41-HP OfficeJet Pro 6970  Infra  6     65 Mbit/s   60      ▂▄▆_  WPA2
        12:59:32:63:8B:FB  --                               Infra  6     65 Mbit/s   37      ▂▄__  WPA2
```

That's it, you're connected automatically until you decide to change networks or diconnect using the following command:
```bash
nmcli device disconnect wlan0
```

If your device isn't wlan0, see your network adapters with the following command:
```bash
nmcli device
```
Which will produce something similar to the following:
```bash
[user@host ~]$ nmcli device
DEVICE          TYPE      STATE         CONNECTION
wlan0           wifi      connected     john-wifi
p2p-dev-wlp4s0  wifi-p2p  disconnected  --
enp3s0          ethernet  unavailable   --
lo              loopback  unmanaged     --
```
Look out for a device with the *TPYE* of **wifi**.

And that's it. Every time you reboot, you'll reconnect to the same wireless network you were last connected to.

If you have any questions, email me at nickolasiaquinta@gmail.com
