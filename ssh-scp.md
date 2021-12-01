# Utilizing a Remote Server
For this guide, I'm going to cover the various ways we can utilize a remote server. This guide gets into SSH, SCP, Apache, and *maybe* FTP. Whether you have a home server or a rented server through some third-party, this advice just works. Regardless if you're connecting to a remove server that runs Windows or Linux, so long as the machine you're using to connect to the remote server is running Linux, everything I tell you here will work. Most of these commands may also work on Windows, but Microsoft has changed a lot of the default programs throughout the years, so don't get your hopes up.

## Enabling Remote Connections
Linux does not enable remote connections by default, nor does Windows (or so we believe). Granted you are running a Linux distro on the remote server that uses systemd, running the following command on your server machine (not your local machine) will enable remote connections from devices on the same network at home:
```bash
systemctl enable sshd
```

Now you will be able to use commands such as ssh and scp to access the remote server via the terminal or shell locally. This is a very powerfull set of tools that gives you the power of two computers in one.

## Finding the Public IP
Every remote server, whether set up at home or set up by a third-party, has a public IP address. Remote servers can be connected to by their domain name, if enabled. The same way you refer to the server as the website name in a web browser, you can connect via ssh like so:
```bash
ssh *user*@*mywebsite*.com
```

However, if you're using a home server, you won't have a domain name by default, and will have to connect via the machine's public IP. This is easy to find, as all you have to do is type the following out on the remote server in the terminal:
```bash
ip addr
```

This should spit out your network interfaces followed by their inet IP. Here is an example:
```bash
1: lo: <LOOPBACK,UP,LOWER_UP```bashmtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: wlan0: <BROADCAST,MULTICAST,UP,LOWER_UP```bashmtu 1500 qdisc mq state UP group default qlen 1000
    link/ether c4:3a:35:c3:c2:99 brd ff:ff:ff:ff:ff:ff
    inet 10.0.0.151/24 brd 10.0.0.255 scope global noprefixroute wlan0
       valid_lft forever preferred_lft forever
```

Let's assume wlan0 is the interface for your remote server, as you're using a wifi card to connect it to your home network. The line that says inet 10.0.0.151/24 denotes the public IP other devices will use to connect to it, regardless if you reach the remote server via a web browser, secure shell, file transfer protocol, etc. This is the IP you would write down:
```bash
10.0.0.151
```

## SSH
Secure Shell connections basically give you the power to control a computer remotely as you would locally via the shell or terminal. Given that you've take a lot of time to learn using shells and temrinals, you'll realize that this is the **most** power you will ever have over a machine, give that the operating system has a lot of utility from the shell in the first place. Most operating systems *do* have a lot of shell utility, but the power you have over Linux from the shell/terminal is going to be much greater than the power you have over Windows 10 from the shell/powershell.

One more important note about connecting via secure shell is that you need to connect via a user that is set up on the remote server. You cannot just connect to a machine, just like you cannot log into Windows, Linux, or Mac without a user. The power you have via SSH also depends on the user privileges the remote user has. If the user you connect as is a sudoer, meaning they are in the wheel group on a Linux instance using sudo, you're going to have nearly as much power as the root user. The abilities of Linux users completely depends on which groups they are a part of. I'll write a guide soon on user groups which will explain these things in greater depth, but for now, understand that your abilities when connected to the remote server are beholden to the groups and privileges of the remote user. Also note, it will not be advisable, nor is it usually possible, to connect to a remote server through the root user. From my experience, ssh has been modified on various distros to prevent this, as this is dangerous.

To use the secure shell, you will type in ssh in the terminal followed by the user you're connecting to the remote machine as followed by *@1.0.0.123*. For instance, let's assume the user that was set up for remote connections to the server from the previous section is *bill*; you would type the following from your local terminal:
```bash
ssh bill@10.0.0.151
```

It'll ask you for bill's password, and once entered, if the connection is successful, you're officially in control over the server from bill's perspective. You can now run all the commands bill can run. You're not going to get GUI programs to work, but any program that bill has access to via the terminal, you now have access to. Any files bill owns and has access to, you now have access to.

## SCP
SSH Secure Copy gives you a second layer of control over your remote server, which is the power to copy files to and from the server. Imagine bill has a library of music in his home directory, and you wanted to listen to it. SSH is not going to give you the power to listen to music remotely, as the file would have to be on your local machine to get an output through any video or audio player. So the solution is to copy the files from the remote server to the local machine.

SCP is actually a two-way command. It copies to and from depending on the arguments you feed the command. Let's say you're copying a file *from* the server to the local machine; bill has a file called darude-sandstorm.mp3 in his music directory. The specific directory would be /home/bill/music/. You're currently in your music folder on your local machine, /home/*user*/music. To copy bill's file to where you're at now, type:
```bash
scp bill@10.0.0.151:/home/bill/music/darude-sandstorm.mp3 .
```

The period-mark at the end implies you want to copy the file to where you are now, and you want to maintain the original name of the file. You could have either specified a different filename in place of the period-mark if you wanted, or you could have also specified a different location on your local machine to place the file.

Now let's say you wanted to stash a file on the remote server that is currently on your local machine. You want bill to have a file called my_script.py in his home folder. Assuming my_script.py is in your current active directory, we would type the following:
```bash
scp my_script.py bill@10.0.0.151:/home/bill/
```

There you have it, bill now has the file in his home directory! If you want to learn more about scp, use *man scp* to read the man page on the extensive list of commands and controls on it.
