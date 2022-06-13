# Restore Qubes OS from Backup
For my specific setup, restoring Qubes OS from a backup is a bit more complicated than pressing a single button. Here's how I do it.

## Step 1: Fresh Install
On a fresh install, go through the usual setup process up until you boot Qubes for the first time. Here, deselect **everything** except for the Debian-11 template. Create a new qube `temp` with this template and mount the USB or partition that holds the backup. Restore **ONLY** `sys-net`. Go to `Qubes Global Settings` and set the update qube, clock qube, and net qube as `sys-net`.

## Step 2: Networking
On my specific machine, I have to update the kernel of sys-net to work with my graphics card. Follow the steps in [WifiToLAN.md](https://github.com/Desperationis/tales-of-linux/blob/main/WifiToLAN.md) to get LAN working from a laptop connected to wifi and run `sudo qubes-dom0-update --enablerepo=qubes-dom0-unstable kernel-latest-qubes-vm` to update the kernel. Change the kernel version of `sys-net` to this version to get the Wifi card working.

## Step 3: Full Restore
At this point you can restore everything, including dom0. To make sure you restore the `/home` directory of dom0 check the option `ignore username mismatch`. In `Qubes Global Settings` set the update qube and net qube as `sys-firewall`. 

## Step 4: Dom0 /home restore
By default the `/home` backup won't overwrite your actual /home. To get all the settings and everything back, you'll need to go the created backup folder (located in `/home`) and move everything out. After this, you'll need to reboot. 

## Optional Step: sys-usb
On my system I have `sys-usb` installed in a particular way with a USB keyboard and mouse such that I have to start it manually. (Possibly) delete the original `sys-usb` and run `sudo qubesctl state.sls qvm.sys-usb` to create a new one and don't touch anything related to the files. **Make extra sure you uncheck the box that makes it start on boot.** If you don't do this, you won't be able to log in and you'll essentially have a bricked system. 

## Optional Step: redshift
To install redshift in dom0 (dangerous I know, but MY EYES) run `sudo qubes-dom0-update redshift`.

## Step 5: Tidying Up
Because the `/home` in the original dom0 was overwritten, some GUI bugs can occur.

To fix **Ghost Qubes** that appear in the application menu but don't actually do anything, go to `~/.config/menus/applications-merged` and delete the ghost qube and reboot.

To fix **Missing Applications** or **Missing Qubes** in the application menu simply go to their settings and refresh their applications. 

Sometimes, disposable qubes are missing applications and can't be fixed with the above methods. So far, the only solution I've found is to recreate them from scratch. 
