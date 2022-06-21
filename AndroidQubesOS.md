# Android on Qubes OS

## Notice
The following would require extra steps in versions earlier than Qubes 4.1 as Qubes would expose the virtual storage as xvdi (or something) and not sda, making it impossible to install.

## The Problem
I **really** need to be able to test specific Android applications to an air-gapped Android VM and be able to transfer files from my other Qubes. 

## Android-x86
One of the first few ISO's I found was Android-x86. However, at first, I wasn't able to get it to boot at all, so I went with Bliss OS. However, due to some mess with setting a custom resolution, Android-x86 ever so slightly the better choice. Here's how to install a Qube with it:

1. [Download](https://www.android-x86.org/) and put it in any AppVM or similar.
2. Create a new Qube and boot from the ISO. Change the disk size now since I'm really unsure if it'd be able to handle a resize. 
3. Go to **Advanced Options** and select **Auto-Install** on the partition you want. As of Qubes 4.1 you'd want to select `sda` as that's the system partition. 
4. Profit.

These steps will give you a fully functional Android Qube. However, you'll be unable to change the size of the Qube as the resolution of the Android is fixed (even if you do software-side resolution changes). To actually change the resolution, you'll need to:

1. Edit the menu.lst (or grub entry) of the system
2. Replace `quiet` with `nomodeset`and add `vga=ask` somewhere. 
3. Reboot machine and you'll be able to set a custom resolution.
4. Profit; Bigger screen.

This method is finicky, and may depend solely on the graphics card you are using. In addition, you'll receive fewer FPS the bigger the screen size you want, as Qubes OS does support the bare-metal OpenGL needed for a crisp experience.

## Bliss OS
Another option I found was [Bliss OS](https://blissos.org/). Not only is it [based on Android-x86](https://www.xda-developers.com/bliss-os-14-android-11-x86-desktop-pc/), but it is also [open-source](https://github.com/BlissRoms-x86) and the pre-installed UI is sexier in my opinion. It's the same steps to intsall as before, but setting the custom resolution has never worked out for me. If I were to do the whole thing again, I would've just chosen Android-x86 instead. 
