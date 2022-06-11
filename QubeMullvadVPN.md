# Qubes Mullvad VPN
VPN's are cool, but setting one up is somewhat of a pain in Qubes OS. But hey, security!

## OpenVPN
**NOTE: Using OpenVPN instead of Wireguard usually results in 50% slower speeds and more overhead.**
This tutorial is based on [the official mullvad guide](https://mullvad.net/en/help/qubes-os-4-and-mullvad-vpn/) and [this guide](https://micahflee.com/2019/11/using-mullvad-in-qubes/) 

1. Create a new `StandaloneVM`, `"MullvadVPN"`, that is based on Debian-11 (as of now). Make sure it is on `sys-firewall` and `Provides Network to other Qubes`. 
2. In any `AppVM`, go to the [Mullvad OpenVPN configuration site](https://mullvad.net/en/account/#/openvpn-config) and download the zip file.
3. Transfer the zip to `MullvadVPN` and unzip it. On this machine, so the following:
4. `sudo apt-get update && sudo apt-get install openvpn`
5. `sudo cp mullvad_config_[whatever]/* /etc/openvpn/`
6. `sudo chmod 755 /etc/openvpn/update-resolv-conf`
7. Then enable the service with `sudo systemctl enable openvpn@mullvad_[whatever].service`
8. [Test your connection to Mullvad](https://mullvad.net/en/check/)
9. Harden your connection by going into `MullvadVPN` qube settings and change `Allow all outgoing connections` to `Limit outgoing connections to` in the firewall tab. Then run `qvm-run --pass-io MullvadVPN 'cat /etc/openvpn/mullvad_[whatever].conf' | grep "remote "` to get the list of connections to mullvad and write those down.
10. If there are any connections you DON'T want, remove them from both the .conf file and the firewall list. 
11. Harden your connection even further by running `qvm-firewall MullvadVPN list` in `dom0`. Find the one with ICMP and run `qvm-firewall MullvadVPN del --rule-no [icmp_rule_#]`. Then block all ICMP connections by running `qvm-firewall MullvadVPN add --before [last_drop_rule_#] drop proto=icmp`. The order of the rules should be **Accept IPS -> Accept DNS -> Drop ICMP -> Drop**. **Note that you won't be able to put in more connections after this through the GUI**


## Wireguard
