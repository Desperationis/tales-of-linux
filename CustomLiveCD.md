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

### Linux Live
[Live Live Kit](https://github.com/Tomas-M/linux-live) is a bundle of shell scrips that turn your entire operating system into a LiveCD. 
