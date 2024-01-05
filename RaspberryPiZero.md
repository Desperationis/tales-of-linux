# Setting up Raspberry Pi Zero W
I've had trouble connecting to the Raspberry Pi Zero W. Tutorials all the way from 2019-2023 did not work in setting up the Raspberry Pi Zero W to a hotspot or via USB tethering. Here is what worked for me. 

### Process
1. In `rpi-imager`, choose "Raspberry PI OS (Legacy, 32-bit) Lite" with Debian Bullseye. 
2. Go to "settings" and fill out hostname, ssh config, wifi config, and locale settings.
3. Right after flashing, go to the `boot` partition in the newly flashed microSD card. Run `touch ssh` to enable ssh on startup. 
4. Plug microSD card into Raspberry Pi Zero W and wait 3-5 minutes. After that, the Zero should be fully setup and ready to connect via ssh.


### Troubleshooting Tips
* **Doesn't connect to wifi #1** - Make sure you are connected to a 2.4gHz network, the regular Zero W can't connect to 5 gHz wifi. 
* **Doesn't connect to wifi #2** - Create a wpa_supplicant.conf file in the boot directory after a fresh flash and put this in it:
* **Doesn't connect to wifi #3** - You forgot to put quotes on the `psk` attribute to denote a cleartext password, not PSK hash

```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=US

network={
     ssid="your_network_name"
     psk="your_wifi_password"
     key_mgmt=WPA-PSK
}
```

* **Error for "ssh might not work if not configured" when connecting** - You forgot to setup SSH in the `rpi-imager` setting menu. 
