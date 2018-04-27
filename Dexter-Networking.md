Dexter has an RJ-45 connector for CAT5 Ethernet connection. Because of issues with the Linux stack, WiFi adapters are not supported (as of April 2018). Although Dexter does have the ability to connect directly to the internet via a CAT5 cable to a local router, the easiest way to connect from a PC is to simply connect that CAT5 cable directly between the network port on the PC and Dexter. This is perfectly acceptable and works very well. The network adapter on the PC should be configured to the 192.168.1. network. Dexter has defaulted to 192.168.1.142 (future versions may DHCP) so any other address is fine for the PC adapter. The PC's wifi adapter can remain connected to local WiFi at the same time. 

Once you are connected, you can shell into Dexter to update the [firmware](Firmware) or do development in the onboard Linux OS, or just connect via [DDE](DDE) to develop and run jobs. 

You generally should NOT need to actually give Dexter access to the internet, but if you need that for some reason, you will need that CAT5 cable to run between Dexter and your Router. After a restart, Dexter should automatically connect and have internet access. You can test that with `ping www.google.com` (press Ctrl+C to stop). If it works, great! If not, here are some common errors and how to resolve them.

- "Network is unreachable" There is no internet connection. Check that Dexter is on the same network as your router. E.g. If your local network is 192.168.0. you will need to edit the IP address in both Dexter and DDE. In Dexter, `sudo nano /etc/network/interfaces`and edit the IP there. In DDE, you will need to change the default address for your Dexter robot in the dde_init.js file in your documents dde_apps folder. e.g. `persistent_set("default_dexter_ip_address", "192.168.0.142")` 

- "Unknown host" The DNS system could not resolve the name to an IP address. Try `ping 8.8.8.8` instead. If you CAN ping 8.8.8.8 but still have no DNS (e.g. ping www.google.com) fails, then try adding 
`nameserver 8.8.8.8`
to a file called  /etc/resolvconf/resolv.conf.d/base
e.g.
`sudo nano  /etc/resolvconf/resolv.conf.d/base `
type in   
`nameserver 8.8.8.8 `
save it, and then restart.   

See also:
- https://help.ubuntu.com/lts/serverguide/network-configuration.html General information on Network configuration for servers. Dexter is basically a server. 