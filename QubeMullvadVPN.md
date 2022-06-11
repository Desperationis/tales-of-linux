# Qubes Mullvad VPN
VPN's are cool, but setting one up is somewhat of a pain in Qubes OS. But hey, security!

## OpenVPN
**NOTE: Using OpenVPN instead of Wireguard usually results in 50% slower speeds and more overhead.**
This tutorial is based on [the official mullvad guide](https://mullvad.net/en/help/qubes-os-4-and-mullvad-vpn/) and [this guide](https://micahflee.com/2019/11/using-mullvad-in-qubes/) 

1. Create a new `StandaloneVM`, `"MullvadVPN"`, that is based on Debian-11 (as of now). Make sure it is on `sys-firewall` and `Provides Network to other Qubes`. 
2. In any `AppVM`, go to the [Mullvad OpenVPN configuration site](https://mullvad.net/en/account/#/openvpn-config) and download the zip file.
3. Transfer the zip to `MullvadVPN` and unzip it. On this machine, run the following:
4. `sudo apt-get update && sudo apt-get install openvpn`
5. `sudo cp mullvad_config_[whatever]/* /etc/openvpn/`
6. `sudo chmod 755 /etc/openvpn/update-resolv-conf`
7. Then enable the service with `sudo systemctl enable openvpn@mullvad_[whatever].service`
8. [Test your connection to Mullvad](https://mullvad.net/en/check/)
9. Harden your connection by going into `MullvadVPN` qube settings and change `Allow all outgoing connections` to `Limit outgoing connections to` in the firewall tab. Then run `qvm-run --pass-io MullvadVPN 'cat /etc/openvpn/mullvad_[whatever].conf' | grep "remote "` to get the list of connections to mullvad and write those down.
10. Look at [https://mullvad.net/en/servers/](If there are any connections you DON'T want, remove them from both the .conf file and the firewall list. 
11. Harden your connection even further by running `qvm-firewall MullvadVPN list` in `dom0`. Find the one with ICMP and run `qvm-firewall MullvadVPN del --rule-no [icmp_rule_#]`. Then block all ICMP connections by running `qvm-firewall MullvadVPN add --before [last_drop_rule_#] drop proto=icmp`. The order of the rules should be **Accept IPS -> Accept DNS -> Drop ICMP -> Drop**. **Note that you won't be able to put in more connections after this through the GUI**


## Wireguard
This tutorial is based on [the official mullvad guide](https://mullvad.net/en/help/easy-wireguard-mullvad-setup-linux/) and [this stackoverflow post](https://superuser.com/questions/1500691/usr-bin-wg-quick-line-31-resolvconf-command-not-found-wireguard-debian).

1. Create a new `StandaloneVM`, `"MullvadVPN"`, that is based on Debian-11 (as of now). Make sure it is on `sys-firewall` and `Provides Network to other Qubes`. 
2. In any `AppVM`, go to the [Mullvad Wireguard configuration site](https://mullvad.net/en/account/#/wireguard-config) and download the config file. Write down the server you use for firewall rules later.
3. Transfer the file to `MullvadVPN`. On this machine, do the following:
4. `sudo apt-get update && sudo apt-get install openresolv wireguard`
5. Move config file to `/etc/wireguard`
6. `sudo chown root:root -R /etc/wireguard && sudo chmod 600 -R /etc/wireguard`
7. Test if the vpn is working by running `wg-quick up [config file name]` and [test your connection to Mullvad](https://mullvad.net/en/check/). If `resolvconf` was not found, run `ln -s /usr/bin/resolvectl /usr/local/bin/resolvconf` to create the symlink and restart the terminal. Run `wg-quick down [config file name]` to bring the vpn down.
8. Run `sudo systemctl enable wg-quick@[config file name]` to enable the service and restart the Qube.
9. Harden your connection by going into `MullvadVPN` qube settings and change `Allow all outgoing connections` to `Limit outgoing connections to` in the firewall tab. Input the IP(s) of the config file(s) that are in use to only allow VPN packets to fly through.
10. Harden your connection even further by running `qvm-firewall MullvadVPN list` in `dom0`. Find the one with ICMP and run `qvm-firewall MullvadVPN del --rule-no [icmp_rule_#]`. Then block all ICMP connections by running `qvm-firewall MullvadVPN add --before [last_drop_rule_#] drop proto=icmp`. The order of the rules should be **Accept IPS -> Accept DNS -> Drop ICMP -> Drop**. **Note that you won't be able to put in more connections after this through the GUI**






