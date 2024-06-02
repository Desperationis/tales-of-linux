# Nvidia Optimus
I have come across many laptops with NVIDIA Optimus, but the software to switch between dedicated and discrete graphics is not that good. Here I provide two options. 

## Envycontrol (Recommended)
[envycontrol](https://github.com/bayasdev/envycontrol) can do all the switching for you, from hybrid, discrete only, and integrated only. This is by far the easiest solution.

## Manual
If you are on Debian 12, [enable non-free packages](https://serverfault.com/questions/240920/how-do-i-enable-non-free-packages-on-debian). 

From [this guide](https://wiki.debian.org/NvidiaGraphicsDrivers#bookworm-525), add `contrib` on there too. Your line in the apt file should look something like this:

```
deb http://deb.debian.org/debian/ bookworm main contrib non-free non-free-firmware
```

After that, run `apt install nvidia-driver firmware-misc-nonfree` and restart your computer. You are done. 

To use optimus, add `__NV_PRIME_RENDER_OFFLOAD=1` as an environment variable before running any GUI application. 

### Firefox Example
Run `__NV_PRIME_RENDER_OFFLOAD=1 __GLX_VENDOR_LIBRARY_NAME=nvidia firefox` and visit [this](https://pmndrs.github.io/detect-gpu/) link. If it tells you NVIDIA, you are done. 


### CUDA
To enable CUDA follow [this](https://wiki.debian.org/NvidiaGraphicsDrivers#Debian_12_.22Bookworm.22-1) link. 
