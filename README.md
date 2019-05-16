# Raspberry Pi DAB Radio
This DAB radio project is based on a Raspberry Pi and the board
[DAB DAB+ FM Digital Radio Development Board Pro with SlideShow](https://www.monkeyboard.org/products/85-developmentboard/85-dab-dab-fm-digital-radio-development-board-pro)
from [MonkeyBoard](https://www.monkeyboard.org/).

## Hardware Conception
This project is still a proof of concept! At the moment the hardware
connections are done on a breadboard. You can see the wiring setup in
the file [`wiring_schematics.pdf`](https://github.com/schlizbaeda/DABradio/blob/master/wiring_schematics.pdf):
* A Raspberry Pi is used as base unit
* A HifiBerry DAC+ is used as audio output because it offers I²S input pins
* The GPIO connections from Raspberry Pi to HifiBerry DAC+ are done by breadboard wires
* The MonkeyBoard (DAB receiver board) is connected to the Raspberry Pi with an USB cable to control it via a serial port (/dev/ttyACM0)
* The I²S bus lines from the MonkeyBoard are connected to the HifiBerry DAC+ directly. The I²S lines of the Raspberry Pi are disconnected.
### Attention!
This project doesn't work on the native audio outputs (HDMI and
3,5mm socket) of the Raspberry Pi!

The I²S audio output of the MonkeyBoard needs an I²S-DAC like the
[PCM5122](http://www.ti.com/product/PCM5122) from Texas Instruments
to convert the digital audio data into analogue signals. To make this
demo DAB radio work you will need an I²S DAC board like for example
the [HifiBerry DAC+](https://www.hifiberry.com/products/dacplus/)
where the I²S connectors are disconnected from the Raspberry Pi and
connected to the MonkeyBoard instead as shown in file
`wiring_schematics.pdf`. Modify the file `/boot/config.txt`for
activating the HifiBerry DAC+ or a compatible board:
```shell
sudo nano /boot/config.txt
```
and change the audio driver like this:
```shell
# Disable audio (loads snd_bcm2835)
#dtparam=audio=on
# Enable the I2S DAC:
dtoverlay=hifiberry-dacplus

```


## Software Installation on the Raspberry Pi
Download the latest [Raspbian Stretch with desktop and recommended software](https://www.raspberrypi.org/downloads/raspbian)
 ("full image") and flash it on a SD card with capacity of 8GB or
higher as described by the Raspberry Pi Foundation [here](https://www.raspberrypi.org/documentation/installation/installing-images/README.md).
Clone this repository onto the Raspberry Pi and start the shell script
[`setup.sh`](https://github.com/schlizbaeda/DABradio/blob/master/setup.sh).


Download the MonkeyBoard demo software for Raspbery Pi:
```shell
wget http://www.monkeyboard.org/images/products/dab_fm/raspberrypi_keystone.tgz
tar -xvzpf raspberrypi_keystone.tgz keystonecomm/
cd keystonecomm/KeyStoneCOMM/
sudo make install
cd ../app/
make
./testdab
```

Download this repository and copy its files into the MonkeyBoard demo app directory:
```shell
cd /home/pi
git clone https://github.com/schlizbaeda/DABradio
cp DABradio/* keystonecomm/app
cd keystonecomm/app
make
./testdab
```
Now the demo application is replaced by the DAB radio from this repository.

## Getting Started
This DAB radio application is a command line based application. It receives
the commands from `stdin` and gives result strings for each command on
`stdout`. So this application can be used as a module using the `stdin` and
`stdout` pipe mechanisms. It's quite easy to create an external GUI application
or a webserver application which is controlled by a web browser as front end
for this DAB radio.

To start the DAB radio, enter these commands:
```shell
open
set volume 9
set stereo 1
list
playstream 14
close
quit
```
or use the prepared file [`get_started.txt`](https://github.com/schlizbaeda/DABradio/blob/master/get_started.txt)
in a pipe redirection:
```shell
cat get_started.txt | ./testdab
```

## Description of the C++ Code
First this application starts another thread to read its commands from
`stdin` in parallel. Then it creates the class `KeyStone` which contains
several methods to control the DAB radio board.

To convert the UTF-16 strings returned by the original KeyStoneCOMM.h
into UTF-8 strings the GNU library [libiconv](https://www.gnu.org/software/libiconv/)
is used.

To get a short help on the `stdin` commands enter the command `help`
inside the DAB radio application.

## Hint
The MOT slideshow feature isn't implemented yet because there are some
issues. It doesn't work properly!

