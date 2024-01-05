# Setting up Waveshare V4 Demo 
The docs for the Waveshare V4 2.13in E-ink display are god awful, so here are the steps on just running the demo.

### Process
1. `sudo apt-get update`
2. sudo apt-get install python3-pip python3-pil python3-numpy
3. sudo pip3 install RPi.GPIO spidev
4. (From [here](https://github.com/waveshareteam/e-Paper/issues/171)) Run `sudo raspi-config`, to `Interfacing Options`, then enable `SPI`. `SPI` is a protocol that allows the raspberry pi to communicate with IC's over short distances. 

### For your own programs
To create programs with the waveshare library with the library globally installed, run `sudo pip install .` in the root directory of the [github](https://github.com/waveshareteam/e-Paper/tree/master/RaspberryPi_JetsonNano/python/) python folder to install the package globally.
