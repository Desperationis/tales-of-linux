# Jetson Nano 20.04 Hack
I wanted to install 20.04-specific programs on the Jetson Nano 2GB version, but NVIDIA has openly declared that an upgrade from 18.04 to 20.04 will never happen. Here is my journey. 

### 1. Install 20.04 image?
Both https://github.com/Qengineering/Jetson-Nano-Ubuntu-20-image and https://forums.developer.nvidia.com/t/xubuntu-20-04-focal-fossa-l4t-r32-3-1-custom-image-for-the-jetson-nano/121768 looked very promising, but both images were made specifically for the 4GB version of the Jetson Nano and it failed to boot.

### 2. Manually Upgrade
Manual upgrading has its risks and I have experienced them first hand. Here is the final **modified** guide I used to upgrade the Jetson based on https://qengineering.eu/install-ubuntu-20.04-on-jetson-nano.html. NOTE: Use the LATEST version of Jetpack; Doing this on 4.4.1 for instance results in the system crashing in 20-40 minutes while on 4.6.1 it's perfectly fine.


1. **Remove any super big programs you don't need** (i.e. libreoffice) as this update takes a few hours to do due to the crappy wifi adapter. 
2. Remove Chromium via `sudo apt-get remove --purge chromium-browser chromium-browser-l10n`. Ubuntu 20.04 does not like it, so you'll have to switch to Firefox instead. 
3. `sudo apt-get update`, `sudo apt-get upgrade`, and `sudo apt-get autoremove`
4. Install a text editor of your choice (i.e. vim) with `sudo apt-get vim`
5. Open `/etc/update-manager/release-upgrades` and set `Prompt=normal`. This will allow you to update to 20.04.
6. `sudo apt-get update` then `sudo apt-get dist-upgrade` to upgrade packages to next sytem.
7. Do NOT RESTART YET. Doing so will most likely prevent you from booting again due to faulty Nvidia drivers. Do `sudo rm /var/run/reboot-required` and `sudo rm /run/reboot-required.pkgs` to bypass the restart check.
8. `sudo do-release-upgrade`. This will take a few hours with some intermittent prompts in between. 
9. Do NOT RESTART YET HERE EITHER. This time it'll prevent you from booting due to no GPU being set on gdm. Go to `/etc/gdm3/custom.conf` and set `WaylandEnable=false`. Then go to `/etc/X11/xorg.conf` and add `Driver "nvidia"` right under `Identifier "Tegra0"`. Finally, go to `/etc/update-manager/release-upgrades` and set `Prompt=never`. 
10. `sudo reboot`

From here on you should have a fully bootable 20.04 system. There are some additional (optional) changes to make:

11. `sudo rm -rf /usr/share/vulkan/icd.d` (if it exists) to remove compiler warnings when using Jtop. 
12. `sudo rm /usr/share/applications/vpi1_demos` (if it exists) to remove circular sym link. 
13. Go to `/etc/apt/sources.list.d` go to each `visionworks*.list`, `cuda*.list`, and `nvidia*.list` and uncomment each source to fix apt update. 
14. If you are using the new GNOME environment, you'll see an enlarged NVIDIA logo on top. To remove this, do `cd /usr/share/nvpmodel_indicator` and `sudo mv nv_logo.svg no_logo.svg`. (This might or might not be a source of system errors, idk, didn't test it too much)

You're done here. If you want to compile CUDA software, then you have to fix the C and C++ compilers and be able to switch between them:

15. `sudo apt-get install gcc-8 g++-8`
16. `sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-9 9` and `sudo update-alternatives --install /usr/bin/gcc gcc /usr/bin/gcc-8 8`
17. `sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-9 9` and `sudo update-alternatives --install /usr/bin/g++ g++ /usr/bin/g++-8 8`

With that, if you ever want to switch between either 8 or 9 in both compilers you simply need to run `sudo update-alternatives --config gcc` or `sudo update-alternatives --config g++`. Make sure that when you do switch, gcc and g++ are the same version to prevent weird compiler errors. In addition, the same process goes with `clang-8`, as 20.04 installs `clang-10`.

Should you ever face an error in `sudo apt-get upgrade`, try to run `sudo dpkg -i --force-overwrite` on the `nvidia-l4t-file` to overrwrite `sleep.conf`. You can keep debugging the issue via `apt --fix-broken`. 

### Power issues
For whatever reason, doing this process on Jetpack 4.4.1 results in a system that crashes almost exactly at 20 minutes (battery power) or 43 minutes (wall power). To solve this issue, you'll have to do the entire process again on Jetpack 4.6.1 (or maybe 4.5, didn't test) where this issue is fixed.

### Wifi on startup
If you want to ssh without having to login via GUI, set "Allow other users" on the GNOME NetworkManager settings to allow all users on the system to use that wifi. This will connect the system to wifi on startup automatically. 





