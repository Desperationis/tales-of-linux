# Custom Live CD
I have spent YEARS finding the perfect solution to creating a custom LiveCD. This will detail why it took so long. 

## Options
### mkusb
The very first option I went for was [mkusb](https://help.ubuntu.com/community/mkusb). While it works exceptionally well for creating LiveCD's, it doesn't allow you to customize it. At most, you have persistence, but this is an Ubuntu-only feature that doesn't work for all distros. 

### Installing to USB
[You could easily install any linux distro on a USB](https://stackoverflow.com/questions/65738318/install-linux-on-flash-disk-like-internal-disk). However, the MAJOR drawback of doing so is the horrendous I/O times. USB's are not meant for very small, frequent, read and writes and so are usually slower than running a HDD. 

You could mitigate this by potentially loading the whole file system in RAM. In fact, this is what [Puppy Linux](https://puppylinux-woof-ce.github.io/) does. Due to being so lightweight, [it loads its entire operating system into RAM](https://forum.puppylinux.com/viewtopic.php?t=5417). 
