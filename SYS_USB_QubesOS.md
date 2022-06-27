# Save Qubes OS from Sys-Usb
You've done it. You've actually locked yourself out of Qubes OS by using `sys-usb` and improperly setting up your USB Keyboard. This tiny guide is meant save you from this exact issue.

## Step 1: Can't make it past decryption screen
If you can't even type out your passcode in the decryption screen, reboot. When starting up, edit the GRUB entry for `Qubes, with Xen Hypervisor` or whatever and delete the `rd.qubes.hide_all_usb` to be able to use ANY usb devices while booting up. Sometimes, you might need to add `qubes.skip_autostart` somewhere after the `quiet`. 

## Step 2: Can't make it past login screen
If you can't make it past your login screen, it means that the `sys-usb` qube is set to automatically boot. Reboot, go into the UEFI / BIOS screen and disable CPU virtualization, then do Step 1 again. You should now be able to make it past the login screen as not a single qube is able to run. Disable `sys-usb` to run on boot, but still keep it just in case.

## Step 3: Persistent Changes
Currently, your system is pretty much fixed, but you'll need to do `Step 1` every single time. To keep them, you'll need to apply the step to ALL grub entries located somewhere in `/boot`. 

## Profit
After this, you should have a fully working system with `sys-usb` now tamed and working once it is *manually* started. You can choose to remove `sys-usb` using the appropiate documentation from Qubes, or not. Your choice. 
