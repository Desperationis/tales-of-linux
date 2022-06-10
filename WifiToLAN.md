# Wifi to LAN
My computer's wifi card doesn't work and I need to update something to fix it, but there is no ethernet port to the router in sight. What am I to do?

## The Issue 
When I installed Qubes OS on my upgraded machine, the wifi card was not detected due to `sys-net` running on an outdated kernel. Due to the heavy security features of Qubes OS, I was unable to manually install the required rpm's to the machine unless I took some dangerous steps to expose the USB stick to `dom0`. Thus, I needed an internet connection through the ethernet port to update the kernel. 

## The Solution 
I didn't know this before, but you can use any other computer with a wifi connection to route the connection through ethernet as described [this post](https://askubuntu.com/questions/359856/share-wireless-internet-connection-through-ethernet). It's actually quite simple with the usual linux GUI:

PC with Wifi Side:
- Open network settings for the ethernet port
- Under IPv4, select `Shared to other computers`. This makes your computer function as a gateway.
- Do the same process with IPv6 if you need it.
- Turn off the connection and turn it back on to generate a new ip address for this machine. 
- Record the ip address of the NIC

PC without Wifi Side:
- Open network settings for the ethernet port
- Under IPv4, select `Manual`.
- In both the DNS address and Gateway address, type in the IP address you recorded earlier. 
- For the netmask, type in `255.255.255.0`.
- Finally, for the IP address for this machine, type in anything as long as the first three digits are the same. (I.e. if `10.42.2.1` is the gateway IP then you can type an address such as `10.42.2.69`.
- Do the same thing for IPv6 if you need it.
- Turn off the connection and turn it back on and you'll be connected.

The reason you have to manually type of the DNS, gateway, netmask, and your own address is because the machine with Wifi is not configured to start and run a DHCP server that does this automatically. While I could write out instructions here how, this is really only meant to be a temporary, quick fix to give something internet and return back to normal. 
