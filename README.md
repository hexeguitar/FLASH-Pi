FLASH-Pi
========
##### SPI & I2C level converter and bus driver for RaspberryPi Zero
The main goal of this project was to create a versatile programmer for various EEPROM and FLASH memory chips using flashrom and wiringPi packages.

![alt text][pic1]

#### Background & origins
In order fo fix an HDMI controller board I had to reprogram the onboard 25-series SPI FLASH memory containing a corrupted firmware image. None of my standalone programmers could erase the chip, so i tried to use the RasPi Zero + Flashrom as a programmer. Flashrom 0.9.9 did detect the chip, but still couldn't erase it. As it turned out, this memory had a non volatile protection bit set, clearing it was not implemented in the software. Using the wiringPi i was able to clear the bit, reprogram the chip and finally set the protection bit back. This gave me an idea that a RasPi Zero W + Flashrom + WiringPi, Python + an addon board with level converters/buffers could serve as a quite powerful, versatile and yet very compact SPI FLASH programmer. Ontop of that i decided to add a similar level converter for the I2C bus using the PCA9617A chip.  
The main function of the FLASH-Pi is to **convert the voltage levels** and **isolate** the SPI and I2C buses on the RaspberryPi Zero.

![alt text][pic2]

#### Features:
* SPI0 bus: level translation between the RPi's native 3.3V and one of the listed below:
    - 5V
    - 3.3V
    - 1.8V
    - externally supplied voltage in range from 1.6V to 5.5V
* I2C1 bus: level translation between the RPi's native 3.3V and one of the listed below:
    - 5V
    - 3.3V
    - 1.8V
    - externally supplied voltage in range from 0.8V to 5.5V
* Overcurrent protection (polyfuses) on the 5V and 3.3V rails supplied by the RPi
* Labelled RaspberryPi GPIO pins for easy wiring and experiments.

#### Circuit design:
Initially i used the TXS0104 chip to level shift SPI bus, which turned out to be a trap (always read the datasheet carefully!). The condition of VCCA (3.3V Raspi side) > VCCB (Target) could not be met for 1.8V output level. Since the SPI lines does not have to be bidirectional i ended up using four SN74LV1T125 buffers specifically designed to up/down translate the levels in wide ranges.  
RaspberryPi provides 5V and 3.3V power rails. Both rails are protected with polyfuses, 100mA for the 5V line and 50mA for the 3.3V one. The 1.8V rail is generated with the MCP1700T-1802 voltage regulator. Target voltage level is set with a 4 way slider switch.  
The 4th option uses an externally supplied target voltage and is intended to use with in-circuit operation.  
I2C section is actually a stock application of the PCA9617A chip. 1k5 pull-ups are provided on both sides.

![alt text][pic5]

#### Software
After doing the usual startup houskeeping (changing the password, setting locales, enabling SPI, I2C, serial port, VNC, Wifi config, etc...) on a fresh Raspbian image i usually remove a few packages that will not be much of use in this application. This will free up some space on the SD card.
```
sudo apt-get remove --purge wolfram-engine scratch minecraft-pi sonic-pi claws-mail bluej python-pygame greenfoot
sudo apt-get clean
sudo apt-get autoremove
```
The next step is to install the wiringPi library. The instruction on how to do that is provided on the authors site:
http://wiringpi.com/download-and-install/

Installing [flashrom](https://www.flashrom.org)   is as easy as:
```
sudo apt-get install flashrom
```
It is probalby worth to install and setup a shared folder with ***samba*** for easy file exchange over the network.

#### Usage
In most cases the RasPi will be run headless and accessed via:
* SSH for command line operation
* VNC viewer using a remote desktop  

![alt text][pic6]

I opted to use the RasPi Zero W for it's compact size and built in WiFi, accessing it remotely from my main PC or laptop. Of course, a regular model Zero will work, too. Especially using an Ethernet over USB-OTG option seems to be quite useful idea - making the device an USB programmer.  

#### PCBs
PCBs are available from OSH Park:  

<a href="https://www.oshpark.com/shared_projects/ycM4MtRF"><img src="https://www.oshpark.com/assets/badge-5b7ec47045b78aef6eb9d83b3bac6b1920de805e9a0c227658eac6e19a045b9c.png" alt="Order from OSH Park"></img></a>  

![alt text][pic4]

------
(c) 11.2017 by Piotr Zapart  
www.hexeguitar.com

License:
Creative Commons - Attribution - ShareAlike 3.0  
[http://creativecommons.org/licenses/by-sa/3.0/](http://creativecommons.org/licenses/by-sa/3.0/)

[pic1]: pics/FlashPi_V2_0.jpg "FLASH-Pi"

[pic2]: pics/FlashPi_V2_2.jpg "FLASH-Pi"
[pic3]: pics/FlashPi_V2_3.jpg "FLASH-Pi"
[pic4]: pics/FlashPi_V2_PCB.jpg "FLASH-Pi PCB"
[pic5]: design_files/FlashPi_V2_schm.png "FLASH-Pi schematic"
[pic6]: pics/FlashPi_VNC3sm.png "Using Flash-Pi via VNC"
