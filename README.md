# How to make a tiny USB powered WiFi Web Server or IoT Device from an ESP8266/Arduino ESP-12S

![Alt text](/doc/Running1.jpg?raw=true "Tiny USB powered ESP-12S")

This shows how to make a little USB powered ESP8266 that can run descretly plugged into USB socket, such as a USB phone charger or power bank, for a cost of just a few pounds.

Have it running as your own little web server, or add sensors to make a tiny IoT device.  

# What you need

![Alt text](/doc/IMAG0607a.jpg?raw=true "Parts")

You need:

1. A USB LED Light

   You can find these from Internet sellers, eg. [Banggood](http://www.banggood.com/0_2W-WhiteWarm-White-Mini-USB-Mobile-Power-Camping-LED-Light-Lamp-p-969441.html) currently have them for less than 50p when buying 3 or more.

2. A 3.3V regulator, eg HT7833

   The SOT-89 package has the correct pinout for this
   
3. An ESP-12S

   This **must** be the "S" version of the ESP-12. The "S" version has built-in pull-up/down resistors and bypass capacitors so you don't need to add these as external components. Eg. these for £1.89 on [EBay](http://www.ebay.co.uk/itm/New-ESP8266-ESP-12S-Serial-Wireless-WIFI-Transceiver-Sender-Receiver-LWIP-AP-STA-/291971729155)

**Note:** I've made a few of these now and have had intermittent problems with one having occasional crashes on restart/wakeups. After trying a few different USB power supplies I've soldered an additional 100uF capacitor across the ESP power supply pins and its now been running fine for a couple of months doing restarts once every 15 minutes. So it appears that even with the "S" version of the ESP-12 module the additional bypass capacitor is needed to ensure stability. 

# Assembly

![Alt text](/doc/Assembly.jpg?raw=true "Assembly")

First remove the resistor and three LED's from the USB stick. They come off quite easily heated up with a soldering iron and lifted with a small screw driver. The LED's are wired in parallel with all the bottom pads connected to the USB +5V and the left pad of the resistor connected to the USB GND.

Next solder on the XC6206 regulator. The SOT-89 package format just happens to fit perfectly where the resistor used to be and with the top tag of the regulator on the middle LED pad, with the correct GND and 5V input. A blob of BlueTack helps get it aligned and held in place while you solder it. 

Next, turn the USB stick over and with a couple of drops of super glue stick on the ESP-12.

Finally add a short wires from each side of the XC6206 to the bottom pins on each side of the ESP-12 for the GND and +3V connections. Also, if its going to use deepSleep then add the connection between the ESP-12 pins 16 and Reset (thats the small white wire on the top right in the photo, connected to the top right pin and the 4th pin down).  

Thats it! You can now plug it into a USB socket it will start running.

# Add environment sensor

You can add small sensors on the back, for example, make an environment sensor by adding a BME280 which measures temperature, air pressure and humidity (about £3 from [AliExpress](https://www.aliexpress.com/item/BME280-Digital-Sensor-Temperature-Humidity-Barometric-Pressure-Sensor-New/32659765502.html)).

***Note***, the ESP8266 uses about 70mA when running so gets noticably warm, which will effect the readings of any temperature sensor mounted on the back. To avoid that the ESP needs to use deep sleep most of the time and only wake up briefly to send the sensor readings. I've found it needs to deep sleep for at least about 3 minutes per sensor publish to avoid the heat problem.   

![Alt text](/doc/BME280.jpg?raw=true "BME280")

# Or a simple temperature sensor with a DS18B20

![Alt text](/doc/DS18B20.jpg?raw=true "DS18B20")

# Or a tiny webserver with 32GB storage

Make a tiny webserver with SD Card storage by adding one of these [Micro SD Card readers](http://www.gearbest.com/development-boards/pp_63013.html) for just £1.

![Alt text](/doc/microsd.jpg?raw=true "ESP with Micro SD")

# Code

You need to program the ESP8266. Mounted on the USB stick like this it is most convenient to use the Over-The-Air (OTA) programming which enables you to "flash" or re-program it wirelessly. However that does still require one first upload over wires to flash it with the OTA sketch.

You need to install the Arduino IDE with ESP8266 support, as described [here](https://github.com/esp8266/Arduino#installing-with-boards-manager).

For the initial code you can just use the BasicOTA sketch that comes as an IDE example. Find that with the IDE menu bar "File -> Examples -> ArduinoOTA -> BasicOTA", and update the sketch with your Wifi netword SSID and password:

![Alt text](/doc/BasicOTA.jpg?raw=true "BasicOTA")

To wire the bare ESP-12 up for flashing is a bit harder than one on a breakout board but its only six connections. Hold the bare ESP-12s in place with a blob of blutack and use male jumper wires which you've tinned with solder, and its not too hard with careful soldering to attach the jumper leads to the ESP-12 pads. You can use the UART chip from another dev module to program it - you need wires on the ESP-12 pins: VCC, GND, TX, RX, RST, and GPIO-0. This is the ESP-12 pinout:

![Alt text](/doc/esp-12.jpg?raw=true "ESP-12")

You connect those to a serial (FTDI) adapter, or if you don't have one of those you can use any ESP8266 development board like a NodeMCU which you've connected CH_EN to GND to disable the ESP8266 on the development board, and then connect the ESP-12 pins (VCC, GND, TX, RX, RST, and GPIO-0) to the same pins on the NodeMCU. Here's what that looks like:     

![Alt text](/doc/Flash.jpg?raw=true "Flash Wiring")

(if all that sounds too hard, [buy one of these](http://www.ebay.co.uk/itm/ESP-12-ESP8266-Programmer-Prototyping-Socket-Adapter-for-Arduino-or-Standalone-/282216409723))

# Example sketch

Once you have your ESP-12 flashed with the OTA sketch you can use it how you like, adding sensors etc, and reflashing it with a new updated sketch - but always keep incluing the OTA code part of the sketch so you can always do OTA updates to the code. 

There is an example sketch [here](https://github.com/HarringayMakerSpace/usb-esp/tree/master/UsbEspDevice) for a IoT type device sending sensor readings to ThingSpeak, uses deep sleep, WiFi Manager for network configuration, and ThingSpeak channel metadata for configuration and OTA firmware downloads from an HTTP url.  
