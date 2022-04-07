# Ubuntu Packages on Pop!\_OS

In rare cases, there are some great Ubuntu packages not able to be installed on Pop!\_OS. Generally, when you try to install those packages on the corresponding Pop!\_OS version, you are greeted with `Repository Does Not Have Release File` or similar.

## Process
My method of fixing this issue is a bit cursed, but by editing `/etc/os-release` and changing the `ID` to `ubuntu`, apt views the system as being its corresponding Ubuntu version. With this, any package that wasn't available with Pop!\_OS but could run on it due to it being based on it is now able to be installed without any issue. Personally, I am yet to come about any fatal errors when doing this. 
