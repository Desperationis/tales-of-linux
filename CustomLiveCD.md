# Custom Live CD
I have spent YEARS finding the perfect solution to creating a custom LiveCD. This will detail why it took so long. 

## Options
### mkusb
The very first option I went for was [mkusb](https://help.ubuntu.com/community/mkusb). While it works exceptionally well for creating LiveCD's, it doesn't allow you to customize it. At most, you have persistence and can customize it from there, but this is an Ubuntu-only (you have to run the tool in a supported version of Ubuntu) feature that doesn't work for all distros. 

### Rufus
[Rufus](https://rufus.ie/en/) is one of the most popular ISO writing tools on Windows. Like mkusb, it offers persistence, but again this does not work for all distros. In addition, doesn't allow you to customize the LiveCD at all. 

### Installing to USB
[You could easily install any linux distro on a USB](https://stackoverflow.com/questions/65738318/install-linux-on-flash-disk-like-internal-disk). However, the MAJOR drawback of doing so is the horrendous I/O times. USB's are not meant for very small, frequent, read and writes and so are usually slower than running a HDD. 

You could mitigate this by potentially loading the whole file system in RAM. In fact, this is what [Puppy Linux](https://puppylinux-woof-ce.github.io/) does. Due to being so lightweight, [it loads its entire operating system into RAM](https://forum.puppylinux.com/viewtopic.php?t=5417). However, this has the drawback of size. If your custom LiveCD is too big, it physically cannot work on other systems. In addition, making it bigger will limit the amount of programs you can run. 

You could potentially buy a portable SSD and install it that way. My goal was to make something that was in the size of a USB and somewhat cheap to reproduce, so external SSDs with an adapter are a no-go. That means I had to buy cheap portable SSDs that are roughly the same size as USBs from weird chinese brands. All of them didn't support booting AT ALL despite weeks of effort and had weird partition table glitches. 

### Linux Live
[Live Live Kit](https://github.com/Tomas-M/linux-live) is a bundle of shell scrips that turn your entire operating system into a LiveCD. However, there are downsides:

- You need to have, on hand, a whole disk to dedicate to making this LiveCD to have the easiest time. You could try a VM however.
- If you don't have a spare disk, you need to make EXTRA sure you have enough space to store the ISO as it essentially doubles the amount of data you use. 
- It doesn't work with all distros.
- [It will not boot into UEFI](https://github.com/Tomas-M/linux-live/issues/218). This issue has been in the project for such a long time, and it was the killer for me. This means it will only boot if your system has CSM enabled. Some motherboards don't even have this feature anymore. 
- Somewhat unmaintained. The developer does work on it time to time, but don't expect your issues (like the UEFI one) to be fixed for a long time.

### Slax OS
[Slax OS](https://www.slax.org/) is an OS specifically meant to be as customizable as possible while providing persistent changes in a LiveCD via memory writes. Cool right? I would've gone with this, but I really wanted an Ubuntu system that had the same look, feel, and features. Currently, Slax OS is based on Debian, not on Ubuntu, so this would not work in my case. 

## Customizing Live CD - Overview
This is the option that worked for me and is what I use today. What I mean by "Customizing Live CD" is to literally rip out the contents of an existing LiveCD, install stuff and customize it, and rebuild the ISO without comprimising ANY features.

I've searched long and far for this precious information. From what I've seen, the exact structure of a LiveCD is not standard and varies from distro to distro, so there's not really a guide for it. When I found [a post on the Ubuntu forum](https://help.ubuntu.com/community/LiveCDCustomization) about this exact issue, I experimented with it rigiously until it worked exactly the way I wanted it to and found how it works. 

**NOTE:** Ironically, after already finishing the whole task, I have found that all LiveCD's follow the [El-Torito](https://en.wikipedia.org/wiki/ISO_9660#El_Torito) standard. Every single one of them. I have read into it too much, but it could help someone else out there in the future.

##### Zeroth Layer
First of all, current LiveCD's (at least the one's from Ubuntu) have two partition tables at the same time: GPT and MBR. This is meant to support booting between old BIOS systems and the newer UEFI systems. How exactly this is done is still a mystery to me, but the results are clearly there. Essentially, the GPT table in the first few sectors of the LiveCD only point to the EFI partition (UEFI uses this partition to find the boot files of the "main" (First Layer) partition) while the MBR lists ALL the partitions and points to the "First layer".

##### First layer
When you open the ISO's ISO partition, every single file you see is not actually meaningful for the system itself. In fact, ALL of it is simply code to mount the read-only `filesystem.squashfs` file that contains a fully working Linux system. This is the first layer, and it's where you'd edit GRUB and such. There's a bunch more boot files in this first layer than in a regular Linux installation due to the need to remain cross-compatible as possible. 

Because of how separated they are, you really only need to make changes in `filesystem.squashfs` with some minor changes in the first layer for customization.

##### Second Layer
This is the `filesystem.squashfs` itself. If you need to make changes to the actual system (packages, files, users, desktop, ect.) it's here. When customizing, it's a matter of unpacking this file, chrooting into it, then inserting it back into the system and making it back into an ISO. 

Now it's time to see this in action.

## Customizing Ubuntu 20.04 LiveCD
These are instructions on modifying an Ubuntu 20.04 Live CD ISO based on [[this post]](https://help.ubuntu.com/community/LiveCDCustomization) from Ubuntu. There are some things I have discarded (due to 20.04 not needing them) and some things I have changed.

**1. Install required tools** ``sudo apt install squashfs-tools xorriso``

**2. Mount Live CD ISO** 
```
mkdir mnt
sudo mount -o loop ubuntu-20.04.4-desktop-amd64.iso mnt
```

**3. Copy ISO to folder** In this specific step, we skip copying down `filesystem.squashfs` since we'll be overriding it anyway.
```
mkdir extract-cd
sudo rsync --exclude=/casper/filesystem.squashfs -a mnt/ extract-cd
```

**4. Expand `filesystem.squashfs`** This step takes the actual Ubuntu environment and expands it to `edit/` so we can edit it as `filesystem.squashfs` is in a read-only filesystem. 
```
sudo unsquashfs mnt/casper/filesystem.squashfs
sudo mv squashfs-root edit
```

**5. Chroot** Here we mount some specific directories into `edit` so that some tools may work when we chroot.
```
sudo mount -o bind /run/ edit/run
sudo mount --bind /dev/ edit/dev
sudo chroot edit
mount -t proc none /proc
mount -t sysfs none /sys
mount -t devpts none /dev/pts
```

**6. Customize** This is the step were you are free to install any packages or customizations as you wish. Mounting `/run` to the chroot environment already gives you internet access, so feel free to go wild. I personally would recommend updating `/etc/apt/sources.list` with your own so that you can find most packages.

**7. Cleanup** When you start running these you should be in your chroot environment. If any of the unmounts fail, continue unmounting then reboot your system.
```
apt clean
rm -rf /tmp/* ~/.bash_history
rm /var/lib/dbus/machine-id
umount /proc || umount -lf /proc
umount /sys
umount /dev/pts
umount /dev
exit
sudo umount edit/run
```

**8. Rebuild Manifests** The LiveCD has specific files that must be updated if `filesystem.squashfs` is ever updated. `filesystem.manifest` holds a list of all packages on the squashfs. `filesystem.size` is the size of the squashfs and is used by the installer to warn the user of not having enough space, ect. `md5sum.txt` is the file that verifies the integrity of Layer 1 (including the squashfs file) every time you reboot your system.
```
chmod +w extract-cd/casper/filesystem.manifest
sudo su
chroot edit dpkg-query -W --showformat='${Package} ${Version}\n' > extract-cd/casper/filesystem.manifest
exit
sudo rm extract-cd/casper/filesystem.squashfs
sudo mksquashfs edit extract-cd/casper/filesystem.squashfs
sudo su
printf $(du -sx --block-size=1 edit | cut -f1) > extract-cd/casper/filesystem.size
exit
cd extract-cd
sudo rm md5sum.txt
find -type f -print0 | sudo xargs -0 md5sum | grep -v isolinux/boot.cat | sudo tee md5sum.txt
cd ..
```

**9. Build ISO** Here is the most complicated step, read carefully. Messing this one up will cost you some functionality such as hybrid booting or a limit on the ISO size.
```
sudo cat <<EOF >xorriso.conf
-as mkisofs \\
-r -J --joliet-long \\
-iso-level 3 \\
-o custom.iso \\
EOF

sudo xorriso -report_about warning -indev "ubuntu-20.04.4-desktop-amd64.iso" -report_system_area as_mkisofs |
    sed -e 's|$| \\|'>>xorriso.conf

echo 'extract-cd' >>xorriso.conf

sudo xorriso -options_from_file xorriso.conf
```

Essentially, `xorriso` is a tool that helps create ISO images. This specific script generates our custom ISO, `custom.iso`, by building it from `extract-cd`. Since the original Ubuntu 20.04 LiveCD's partition table is very complicated due to being a hybrid boot of both MBR and UEFI, `xorriso` has been told to combine the partitions and partition tables of the original `ubuntu-20.04.4-desktop-amd64.iso` to `custom.iso` to skip the headache entirely. 

The ISO 9660 standard holds specific "Levels of Interchange" that actually limit the size of the ISO. This can be set in `xorriso` with `-iso-level`. `iso-level 3` is the highest and can store up to TB. Levels 1 and 2 can only hold 4 GB, however, and is actually the default had it not been set in this script. 

## Conclusion
And that's it! You should now have a fully functional custom Ubuntu 20.04 LiveCD. You can try skipping the installer so you don't have to press "Try Me", the the user name, default wallpaper, boot logo, ect. 




