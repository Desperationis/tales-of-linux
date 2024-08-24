## Setup any Linux System as a Permanent Access Point
This was mainly used to configure a Raspberry Pi to always act as an access point, even when the system reboots. To put it simply, just run these commands:

```
sudo apt install dnsmasq-base -y
nmcli connection add type wifi ifname wlan0 con-name access_point autoconnect yes ssid MYSSID
nmcli connection modify access_point 802-11-wireless.mode ap 802-11-wireless.band bg ipv4.method shared
nmcli connection modify access_point wifi-sec.key-mgmt wpa-psk
nmcli connection modify access_point wifi-sec.psk "MYPASSWORD"
nmcli connection up access_point
```

First step may not be necessary. It was based on this post: https://askubuntu.com/questions/1460268/how-do-i-setup-an-access-point-that-starts-on-every-boot


To undo this, here's what you do:

```
nmcli connection delete access_point
```

It's that simple!


