Table of contents:
- <a href="#connection"> Connecting Dexter to the Network </a>
- <a href="#ip-address"> Finding Dexter on the Network, IP address </a>
- <a href="#shell-access-via-ssh"> Shell access via SSH </a>
- <a href="#x-windows"> Remote GUI interface via X-Windows </a>
- <a href="#file-sharing"> Reading / Writing files</a>
- <a href="#internet-access"> Letting Dexter access the Internet </a>
- <a href="#see-also"> See also </a>

# Connection
Dexter has an RJ-45 connector for CAT5 Ethernet connection. Because of issues with the Linux stack, WiFi adapters are not supported (as of September 2018). Although Dexter does have the ability to connect directly to the internet via a CAT5 cable to a local router, the easiest way to connect from a PC is to simply connect that CAT5 cable directly between the network port on the PC and Dexter. This is perfectly acceptable and works very well. The network adapter on the PC should be configured to the 192.168.1. network. 

# IP Address
## Fixed IP
Dexter normally defaults to 192.168.1.142 so any other address is fine for the PC adapter. The PC's wifi adapter can remain connected to local WiFi at the same time as long as it's assigned IP address is in the same subnet. 

To enable both WiFi and CAT5 to Dexter when the WiFi local network isn't on 192.168.1.#: First, download PuTTY or other SSH software (see below for setup and use). Make a note of the local IP address assigned to the WiFi then disconnect from WiFI, CAT5 to Dexter, SSH into Dexter or use a [Serial USB connection](Dexter-USB-Connection). At the command prompt:
`nano /etc/network/interfaces`
and edit the ip address to 192.168.#.142 where the # matches the network IP address. Ctrl+X to exit, and follow the prompts to save. 

## DHCP assigned
Some Dexters may have been configured to use DHCP, and so need to be CAT5 cabled to a router with a DHCP service, or to a network with a DHCP server, and it's IP address will be whatever is assigned by DHCP. Your computer configuration needn't change. You can log into your router and look at the connected devices to find it, or
- Android or iOS Smartphone: Install and use the Fing app<BR>
https://www.fing.io/
- on Linux / Mac use `arp -a`. You may need to ping broadcast first. Or `sudo apt-get nmap` and then `nmap -rP _ip_/24` where _ip_ is your local network address. e.g. 192.168.1.1
- on Windows, `arp -a` returns the ip address, but no machine names. Or you can download and install<BR>
http://www.advanced-ip-scanner.com

If you don't have a CAT5 connection to the network, you can connect directly via CAT5 to your computer, after making some changes to Dexters networking. See [Issue 37](https://github.com/HaddingtonDynamics/Dexter/issues/37) for more information. You may need to use a [Serial USB connection](Dexter-USB-Connection) to gain access to make those changes.

To disable DHCP and return to a static IP address, connect to Dexter and at the command line, enter<BR>
`nano /etc/network/interfaces`
<BR>Then edit the file to change the `iface eth0` line to replace `dhcp` with `static`. It should look like this:<BR>
````
auto eth0
iface eth0 inet static
    address 192.168.1.142
````

# Shell Access via SSH
Once you are connected, you can shell into Dexter to update the [firmware](Firmware) or do development in the onboard Linux OS, or just connect via [DDE](DDE) to develop and run jobs. 

To **SSH into Dexter**, you will need an [SSH client](https://en.wikipedia.org/wiki/Comparison_of_SSH_clients) (e.g. [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) or [Remmina](https://remmina.org/) for Linux ). Configure the IP address in the client software to [Dexter's IP address](Dexter-Networking#ip-address), set the port to 22, and select SSH. 

Username: `root`
<br>password: `klg`

Hint: Under Connection / Data you can specify the "Auto-login username" so you don't have to type root every time. 

# X-Windows
It is also possible to run GUI applications via X11 Forwarding either from SSH on Linux or on Windows, from PuTTY via an X-Windows Server program such as [X-Ming](https://en.wikipedia.org/wiki/Xming).

On Linux, just SSH in but add the -X parameter, as in `ssh root@192.168.1.142 -X` and it will forward all X-Windows commands back to the local PC. 

In PuTTY, under Connection / SSH / X11, check "Enable X11 forwarding", set the display location to `localhost:0.0` then save that session. 

## Individual applications
In Linux, just run any GUI application on Dexter. On Windows, you can just start XMing, then and it will start a big blank "desktop" window. Alt tab to see the Windows taskbar, and minimize the empty desktop (you can't close it) by right clicking the preview from the taskbar and selecting "Minimize". Then from Dexter and start "nautilus" from the command line, but use "nautilus &" to run it in the background. Navigate to the /srv/samba/share folder to work, or to the /usr/share/applications folder to launch other applications. You can "Bookmark" these folders for faster access. This is kind of nice, because the applications each run in their own window in Windows making it easy to arrange them. 

## Desktop / Command line
You can also start the standard Ubuntu desktop with the command: `gnome-session --session=ubuntu-2d &` or for a lighter desktop, try `/usr/bin/startlxde &`. But from Windows, if you have multiple monitors, you may wish to configure XMing to use only one monitor. XMing comes with XLaunch which allows you to configure how XMing display things. On the first "Display settings" panel, select "One window" and then keep the defaults for the other panels. Save that configuration file and use it to launch XMing in the future. 

In Linux, the Dexter desktop show up on the PC desktop, mixing both on the same screen. Luckily, the XDE desktop `/sr/bin/startlxde &` uses the bottom and left corner, and the Ubuntu desktop uses the top and left side, so they co-exist pretty well. It's fun keeping track of which application is running on which device.

## Easy Desktop
But the easiest way to set up the full desktop is to use XLaunch to make a workable config file for XMing and then use that config to launch it. 
- On the first "Display settings" panel, select "One window", on display 0. That zero is port 6000. e.g. 1 would be port 6001. If you use the "...without titlebar" version, you can't move the window. You can still alt tab, but if you have multiple monitors, it's nice to be able to put it elsewhere. Click "Next"
- On the "Session type" panel select "Start a program", click "Next"
- On the "Start Program" panel set the "Start program" to `gnome-session --session=ubuntu-2d` and under "Run Remote" do "Using PuTTY" then put in the IP address of Dexter under "Connect to computer" and also put in root and klg as the user and pass. Click "Next"
- Leave the Clipboard and other stuff as default. Click "Next"
- On the last "Finish" panel, check "Include PuTTY  Password as insecure plain text" and save the config.
- It will automatically start when you hit the "Finish" button. It does take some time to start; ~10 seconds. In the future, you can just XLaunch your configuration file. 

Once in the desktop, you can start the file manager and navigate to the /srv/samba/share folder to edit files, or to the /usr/share/applications folder to launch other applications. This works a bit faster than the standard Dash home. If you "Browse Network" you may be able to access local NAS or other Linux shares, but Windows shared folders probably won't appear. 

The default background logo really slows things down, but you can get rid of it under System Settings / Appearance. A solid black color seems fastest.

Terminal is very nice looking, with colors, a clear font, and nice size. It's even nicer if you go into Edit / Profile Preferences and uncheck "Use the system fixed width font".

# File Sharing
## SAMBA
Dexter provides a SAMBA share and an sFTP service for transferring files back and forth, in addition to the socket connection for commands via DexRun. Access the share at `\\<dexters-ip>\share`. Some versions of Windows or some Firewalls may block access to this share. Apparently this is because it isn't password protected.

## SFTP
You can also transfer files via the `/usr/lib/openssh/sftp-server` service but you will need to supply the same username and password. And you will need a client which supports sftp such as<BR>
https://winscp.net/eng/download.php
<BR>Or if you want to integrate SFTP support into the Windows Explorer, you might try:<BR>
http://www.swish-sftp.org
<BR>Once installed, just go to "Swish" in the address bar and "Add SFTP Connection". The [Remmina](https://remmina.org/) SSH client (only available on Linux) includes a file transfer function.

If you wish to disable or reconfigure the sftp daemon, see the /etc/ssh folder. Note: SFTP is not at all related to FTP.

# Internet access
You generally should NOT need to actually give Dexter access to the internet, but if you need that for some reason, you will need that CAT5 cable to run between Dexter and your Router. After a restart, Dexter should automatically connect and have internet access. You can test that with `ping www.google.com` (press Ctrl+C to stop). If it works, great! If not, here are some common errors and how to resolve them.

- "Network is unreachable" There is no internet connection. Check that Dexter is configured and on the same network as your router. E.g. If your local network is 192.168.0. you will need to edit the IP address in both Dexter and DDE. In DDE, you will need to change the default address for your Dexter robot in the dde_init.js file in your documents dde_apps folder. e.g. `persistent_set("default_dexter_ip_address", "192.168.0.142")`. In Dexter, `sudo nano /etc/network/interfaces`and edit the IP there to match. You will also need to make sure the correct gateway and nameservers are set. A typical setup looks like this:
````
address 192.168.1.142
#set Dexters fixed IP address here
netmask 255.255.255.0
gateway 192.168.1.1
#set your gateway address (router)
dns-nameservers 192.168.1.1 8.8.8.8
#set your local DNS server and/or 8.8.8.8 to use google
````

- "Unknown host" The DNS system could not resolve the name to an IP address. Try `ping 8.8.8.8` instead. If you CAN ping 8.8.8.8 but still have no DNS (e.g. `ping www.google.com`) fails, then try adding 
`nameserver 8.8.8.8`
to a file called  /etc/resolvconf/resolv.conf.d/base
e.g.
`sudo nano  /etc/resolvconf/resolv.conf.d/base `
type in   
`nameserver 8.8.8.8 `
save it, and then restart.   

# See also
- https://help.ubuntu.com/lts/serverguide/network-configuration.html General information on Network configuration for servers. Dexter is basically a server. 
- https://askubuntu.com/questions/971171/how-to-use-putty-to-get-x11-connections-over-ssh-from-windows-to-ubuntu Setting up X Windows on Windows via PuTTY.
- http://www.straightrunning.com/XmingNotes/ XMing home page.