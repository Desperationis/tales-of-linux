# Custom Live CD
I have spent YEARS finding the perfect solution to creating a custom LiveCD. This will detail why it took so long. 

## Options
### mkusb
The very first option I went for was [mkusb](https://help.ubuntu.com/community/mkusb). While it works exceptionally well for creating LiveCD's, it doesn't allow you to customize it. At most, you have persistence, but this is an Ubuntu-only (you have to run the tool in a supported version of Ubuntu) feature that doesn't work for all distros. 

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











