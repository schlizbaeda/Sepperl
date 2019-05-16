# Raspberry Pi DAB Radio
This DAB radio project is based on a Raspberry Pi and the board
[DAB DAB+ FM Digital Radio Development Board Pro with SlideShow](https://www.monkeyboard.org/products/85-developmentboard/85-dab-dab-fm-digital-radio-development-board-pro)
from [MonkeyBoard](https://www.monkeyboard.org/).

## Hardware Conception
This project is still a proof of concept. The hardware is only built on
a breadboard. You can see the setup in the file [`wiring_schematics.pdf`](https://github.com/schlizbaeda/DABradio/blob/master/wiring_schematics.pdf):
* A Raspberry Pi is used as base unit
* A HifiBerry DAC+ is used as audio output because it offers I²S input pins
* The GPIO connections from Raspberry Pi to HifiBerry DAC+ are done by breadboard wires
* The MonkeyBoard (DAB receiver board) is connected to the Raspberry Pi with an USB cable to control it via a serial port (/dev/ttyACM0)
* The I²S bus lines from the MonkeyBoard are connected to the HifiBerry DAC+ directly. The I²S lines of the Raspberry Pi are disconnected.
### Attention!
To make it work you must use a I²S DAC board which is connected to the I2S bus of the Monkeyboard as shown in the file `wiring_schematics.pdf`. 

## Software Installation on the Raspberry Pi
Download the latest [Raspbian Stretch Full Image](https://www.raspberrypi.org/downloads/raspbian) and flash it on a SD card >= 8GB as described by the Raspberry Pi Foundation [here](https://www.raspberrypi.org/documentation/installation/installing-images/README.md).

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

