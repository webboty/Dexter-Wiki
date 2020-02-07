Table of contents:
- <a href="#connection"> Connecting Dexter to the Network </a>
- <a href="#ip-address"> Finding Dexter on the Network, IP address </a>
- <a href="#shell-access-via-ssh"> Shell access via SSH </a>
- <a href="#x-windows"> Remote GUI interface via X-Windows </a>
- <a href="#file-sharing"> Reading / Writing files</a>
- <a href="#internet-access"> Letting Dexter access the Internet </a>
- <a href="#see-also"> See also </a>

# Connection
Dexter has an RJ-45 connector for CAT5 Ethernet connection. Because of issues with the Linux stack, WiFi adapters have not been supported (before Feb 2018). Those were resolved on [the OS 16.04 image](https://github.com/HaddingtonDynamics/Dexter/issues/25), and WiFi adapters DO work, but can be difficult to use. (see below)

## Direct to PC
Although Dexter does have the ability to connect directly to the internet via a CAT5 cable to a local router, the easiest way to connect from a PC is to simply connect that CAT5 cable directly between the network port on the PC and Dexter. This is perfectly acceptable and works very well. The network adapter on the PC should be configured to the 192.168.1. network. (Since 201903, the [SD card image](https://github.com/HaddingtonDynamics/Dexter/wiki/SD-Card-Image) supports both 192.168.1 and 192.168.0 networks at the same time). 

For instructions to configure your PC's CAT5 Network adapter, go into [DDE](DDE) or go to [the HDRobotic.com Software page](http://hdrobotic.com/software) and on the Doc panel, under **User Guide**, **Configure Dexter**, **Data Connection**, follow the directions for your operating system.

Note: You may find that the WiFi network and the Dexter network need to be on seperate subnets. E.g. If WiFI is using 192.168.1. something, then try setting the CAT5 adapter for Dexter to 192.168.0.142

In most cases, for most users, this is all that is required. You can start using [DDE](DDE) to program your robot. For other options, continue on here... 

## Direct to Network Router
If you can connect a CAT5 cable between Dexter and your network router, Dexter will have access to NTP time, and can be reached from any PC in your internal network (assuming your router allows it). If your network is 192.168.1 or 192.168.0 this should work as long as no other device is at 192.168.x.142. You may need to change Dexters fixed IP address, or change Dexter to DHCP. (see below)

## WiFi 
If your [SD Card](SD-Card-Image) is up to date, you should be able to install a WiFi adapter via the USB Host connection on the [MicroZed board](MicroZed), and configure it for access to your WiFi router. However, 
1. The WiFi adapter will overheat next to the stepper drivers, so it's best to install it via a USB A extension cable. e.g. [USB 3.0 A Male to A Female 6" extension cable](https://www.amazon.com/KabelDirekt-Extension-Connect-Socket-Suitable/dp/B07D8Y6MNX)
2. The USB connector is not reachable if a fan is installed directly over the stepper drivers, so that will need to be moved back. There are holes in the motor driver PCB below the terminal blocks that work well for stand-offs. 
3. You will need to connect via CAT5 or [Serial USB connection](Dexter-USB-Connection) then [SSH in](shell-access-via-ssh) to Dexter and configure the SSID and password to match your router. 
4. Use `ifconfig` to verify that the WiFi adapter is working and available as a network adapter device. It should show up after the standard "eth0" and "lo" adapters with a name starting with "w" followed by a large hexadecimal number e.g. "w1x74de38aef0ea" which is based on the mac address of the adapter. <BR>[(screen shot)](https://user-images.githubusercontent.com/419392/67534526-56dd4080-f683-11e9-87b8-1a84c22a2c8d.png)
5. Use `nmtui` from the command line and follow the prompts to connect to your WiFi. It should automatically reconnect and "just work". First, use the arrow keys to "Activate a connection" <BR>
![](https://user-images.githubusercontent.com/419392/67534741-64df9100-f684-11e9-8679-df477c2321a2.png)<BR>
Then select the WiFi SSID <BR>
![](https://user-images.githubusercontent.com/419392/67534796-acfeb380-f684-11e9-81fc-42a530e2b3dd.png)<BR>
A popup will ask you for the password, enter that. It should now appear with a "*" to the left of the SSID name, indicating it's connected. Use the right arrow and down arrow to select Quit from the program. 
Use `ifconfig` again to verify that there is an ip address listed under the adapter entry. That is Dexter's WiFi IP address.<BR>
![](https://user-images.githubusercontent.com/419392/67534884-0e268700-f685-11e9-9cb1-673706d8b68b.png)

If you have issues, you can try `sudo nmcli dev wifi connect "ESSID_NAME" password "ESSID_PASSWORD"` from the command line to add a new connection to those Dexter knows. For more `nmcli` commands see below. Or setup <a href="#x-windows">Remote GUI interface via X-Windows</a> and use the network manager. It's icon is in the bottom right (just left of the time) on Dexter's desktop.

Note: `sudo iwconfig wlan0 essid "<name>" key s:"<password>"` does NOT support WPA authentication.

Units known to work:
- [EDIMAX EW-7811Un](https://www.amazon.com/Edimax-EW-7811Un-150Mbps-Raspberry-Supports/dp/B003MTTJOY) It does get hot and may fail just from overheating. Probably anything with the same chipset (RTL8188CUS) will work.
- [Adafruit OURLiNK 150M](https://www.adafruit.com/product/1012). Probably anything with the RTl8192cu chipset will work.

Units known to NOT work:
- [Panda brand](https://www.amazon.com/Panda-300Mbps-Wireless-USB-Adapter/dp/B00EQT0YK2/) does NOT work. No pre-compiled driver available and no coherent documentation or source for a driver.

### Using nmcli to setup WiFi on Dexter.
nmcli is the command line version of the network manager GUI. It can be used to set up WiFi on Dexter.
- `nmcli dev wifi` to see WiFi access points available to Dexter.
- `nmcli dev wifi connect ESSID_NAME password ESSID_PASSWORD` to add a new connection to those Dexter knows.
- `nmcli nm` to see the current status of Dexter's WiFi.
- `nmcli c` shows a list of connections known to Dexter. 
- `nmcli c delete id CONNECTION_NAME` will delete the specified connection from Dexter's memory. 

### Automatic WiFi connection by Dexter
To automatically connect to a WiFI access point on startup, you can SSH into Dexter, then `sudo nano /etc/network/interfaces` and add the necessary information there. It should look something like
````
auto wlan0
iface wlan0 inet static
address ASSIGNED_IP
netmask 255.255.255.0
gateway THE_GATEWAY
wireless-essid YOURSSID
wireless-key WIRELESSKEY_HERE
````

See also: 
- https://askubuntu.com/questions/16584/how-to-connect-and-disconnect-to-a-network-manually-in-terminal

# IP Address
## Fixed IP
Dexter normally defaults to 192.168.1.142 so any other address is fine for the PC adapter. (Since 201903, the [SD card image](https://github.com/HaddingtonDynamics/Dexter/wiki/SD-Card-Image) answers on both 192.168.1.142 and 192.168.0.142 at the same time) The PC's wifi adapter can remain connected to local WiFi at the same time as long as it's assigned IP address is in the same subnet. 

To enable both WiFi and CAT5 to Dexter when the WiFi local network isn't on 192.168.1.#: First, download PuTTY or other SSH software (see below for setup and use). Make a note of the local IP address assigned to the WiFi then disconnect from WiFI, CAT5 to Dexter, SSH into Dexter or use a [Serial USB connection](Dexter-USB-Connection). At the command prompt:
`nano /etc/network/interfaces`
and edit the ip address to 192.168.#.142 where the # matches the network IP address. Ctrl+X to exit, and follow the prompts to save. 

## DHCP assigned
Some Dexters may have been configured to use DHCP, and so need to be CAT5 cabled to a router with a DHCP service, or to a network with a DHCP server, and it's IP address will be whatever is assigned by DHCP. Your computer configuration needn't change. You can log into your router and look at the connected devices to find it, or
- Android or iOS Smartphone: Install and use the Fing app<BR>
https://www.fing.io/
- on Linux / Mac use `arp -a`. You may need to ping broadcast first. Or `sudo apt-get nmap` and then `nmap -rP _ip_/24` where _ip_ is your local network address. e.g. 192.168.1.1
- on Windows, `arp -a` returns the ip address, but no machine names. The MAC address of the CAT5 adapter will always start with "00:5D:03" so you can do `arp -a | find "00-5d-03"` and the result is almost certainly your Dexter. Or you can download and install<BR>
http://www.advanced-ip-scanner.com

If you don't have a CAT5 connection to the network, you can connect directly via CAT5 to your computer, (see above) but if Dexter has been setup for DHCP, you will need to make some changes to Dexters networking. See [Issue 37](https://github.com/HaddingtonDynamics/Dexter/issues/37) for more information. You may need to use a [Serial USB connection](Dexter-USB-Connection) to gain access to make those changes.

To disable DHCP and return to a static IP address, connect to Dexter and at the command line, enter<BR>
`nano /etc/network/interfaces`
<BR>Then edit the file to change the `iface eth0` line to replace `dhcp` with `static`. It should look like this:<BR>
````
auto eth0
iface eth0 inet static
    address 192.168.1.142
````

To enable DHCP, just change the `static` to `dhcp`. It should look like this:<BR>
````
auto eth0
iface eth0 inet dhcp
    address 192.168.1.142
````

The `address` line shouldn't cause any problems, but you can always comment it out by adding a `# ` at the start of the line.
# Shell Access via SSH
Once you are connected, you can [shell into Dexter](SSH-into-Dexter) to update the [firmware](Firmware) or do development in the onboard Linux OS, or just connect via [DDE](DDE) to develop and run jobs. 

On Windows prior to Windows 10, to **[SSH into Dexter](SSH-into-Dexter)**, you will need an [SSH client](https://en.wikipedia.org/wiki/Comparison_of_SSH_clients) (e.g. [PuTTY](https://www.chiark.greenend.org.uk/~sgtatham/putty/) 64 bit for a 64 bit OS, or [Remmina](https://remmina.org/) ). Configure the IP address in the client software to [Dexter's IP address](Dexter-Networking#ip-address), set the port to 22, and select SSH. 

Username: `root`
<br>password: `klg`

Hint: Under Connection / Data you can specify the "Auto-login username" so you don't have to type root every time. 

On Linux or Mac or from Windows 10, from the Terminal application / cmd prompt type: `ssh root@192.168.1.142` (or whatever IP address Dexter is on) and when prompted for the password, enter `klg`.

# X-Windows
It is also possible to run GUI applications via X11 Forwarding either from SSH on Linux or on Windows, from PuTTY via an X-Windows Server program such as [X-Ming](https://en.wikipedia.org/wiki/Xming).

On Linux, just SSH in but add the -X parameter, as in `ssh root@192.168.1.142 -X` and it will forward all X-Windows commands back to the local PC. 

On the MAC `ssh -XCY root@192.168.1.142`

In PuTTY, under Connection / SSH / X11, check "Enable X11 forwarding", set the display location to `localhost:0.0` then save that session. 

## Individual applications
In Linux, just run any GUI application on Dexter. On Windows, you can just start XMing, then and it will start a big blank "desktop" window. Alt tab to see the Windows taskbar, and minimize the empty desktop (you can't close it) by right clicking the preview from the taskbar and selecting "Minimize". Then from Dexter and start "nautilus" from the command line, but use "nautilus &" to run it in the background. Navigate to the /srv/samba/share folder to work, or to the /usr/share/applications folder to launch other applications. You can "Bookmark" these folders for faster access. This is kind of nice, because the applications each run in their own window in Windows making it easy to arrange them. 

## Desktop / Command line
You can also start the standard Ubuntu desktop with the command: or for a lighter desktop, try `/usr/bin/startlxde &` or `/usr/bin/lxsession -s Lubuntu -e LXDE &`. But from Windows, if you have multiple monitors, you may wish to configure XMing to use only one monitor. XMing comes with XLaunch which allows you to configure how XMing display things. On the first "Display settings" panel, select "One window" and then keep the defaults for the other panels. Save that configuration file and use it to launch XMing in the future. 

Note: On Ubuntu 12, we could do `gnome-session --session=ubuntu-2d &`  but that seems to not work on 16.

In Linux, the Dexter desktop show up on the PC desktop, mixing both on the same screen. Luckily, the XDE desktop `/usr/bin/startlxde &` uses the bottom and left corner, and the Ubuntu desktop uses the top and left side, so they co-exist pretty well. It's fun keeping track of which application is running on which device.

In the Mac, the XQuartz application shows up at the top.

## Easy Desktop
But the easiest way to set up the full desktop, _after doing the above_, is to use XLaunch to make a workable config file for XMing and then use that config to launch it. 
- On the first "Display settings" panel, select "One window", on display 0. That zero is port 6000. e.g. 1 would be port 6001. If you use the "...without titlebar" version, you can't move the window. You can still alt tab, but if you have multiple monitors, it's nice to be able to put it elsewhere. Click "Next"
- On the "Session type" panel select "Start a program", click "Next"
- On the "Start Program" panel set the "Start program" to `/usr/bin/startlxde` and under "Run Remote" do "Using PuTTY" then put in the IP address of Dexter under "Connect to computer" and also put in root and klg as the user and pass. Click "Next"
- Leave the Clipboard and other stuff as default. Click "Next"
- On the last "Finish" panel, check "Include PuTTY  Password as insecure plain text" and save the config.
- It will automatically start when you hit the "Finish" button. It does take some time to start; ~10 seconds. In the future, you can just XLaunch your configuration file. 

If you get a "key exchange" error, see this:<BR>
https://www.opentechguides.com/askotg/question/106/putty-fatal-error-expected-key-exchange-group-packet-from-server



Once in the desktop, you can start the file manager and navigate to the /srv/samba/share folder to edit files, or to the /usr/share/applications folder to launch other applications. This works a bit faster than the standard Dash home. If you "Browse Network" you may be able to access local NAS or other Linux shares, but Windows shared folders probably won't appear. 

The default background logo really slows things down, but you can get rid of it under System Settings / Appearance. A solid black color seems fastest.

Terminal is very nice looking, with colors, a clear font, and nice size. It's even nicer if you go into Edit / Profile Preferences and uncheck "Use the system fixed width font".

# File Sharing

## SAMBA
Dexter provides a SAMBA share and an sFTP service for transferring files back and forth, in addition to the socket connection for commands via DexRun. Access the share at `\\<dexters-ip>\share`. Some versions of Windows or some Firewalls may block access to this share. Apparently this is because it isn't password protected. See [Issue #58](https://github.com/HaddingtonDynamics/Dexter/issues/58) for issues with Windows 8 / 10. 

## SFTP
You can also transfer files via the `/usr/lib/openssh/sftp-server` service but you will need to supply the same username and password. 

On Windows you will need a client which supports sftp such as<BR>
https://winscp.net/eng/download.php
<BR>Or if you want to integrate SFTP support into the Windows Explorer, you might try:<BR>
http://www.swish-sftp.org
<BR>Once installed, just go to "Swish" in the address bar and "Add SFTP Connection". 

The [Remmina](https://remmina.org/) SSH client (only available on Linux) includes a file transfer function. Be sure to select SFTP as the protocol and change the port to 22. 

On the MAC you can use Cyberduck. Click on "Open Connection" then change "FTP.." to "SFTP..." and enter the ip address of in the "Server" field. Username "root" and password "klg". The port must be set to 22. 

If you wish to disable or reconfigure the sftp daemon, see the /etc/ssh folder. Note: SFTP is not at all related to FTP.

## SCP
If you are using Linux or MAC, you can transfer files via the terminal window using the SCP command. For example, if you cd into the folder on your computer where the file you want to transfer is located, you can enter a command like: <BR>
`scp -3 Find_Index_Pulses_HDI.dde root@192.168.1.142:/srv/samba/share`
<BR> where "Find_Index_Pulses_HDI.dde" is the file being transferred, "192.168.1.142" is the IP address of the Dexter, and "/srv/samba/share" is the destination address.

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

- The ethernet adapter on the Xilix chips all come with the same MAC address and IP address. If there is more than one Dexter on the CAT5 network (doesn't apply to WiFi) you will need to edit both. See [Issue 57](https://github.com/HaddingtonDynamics/Dexter/issues/57) for more information. 

# See also
- https://help.ubuntu.com/lts/serverguide/network-configuration.html General information on Network configuration for servers. Dexter is basically a server. 
- https://askubuntu.com/questions/971171/how-to-use-putty-to-get-x11-connections-over-ssh-from-windows-to-ubuntu Setting up X Windows on Windows via PuTTY.
- http://www.straightrunning.com/XmingNotes/ XMing home page.