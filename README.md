# ESP32 oscilloscope with Web user interface - see the signals the way ESP32 sees them

**Demo ESP32 server is available at [http://193.77.159.208/oscilloscope.html](http://193.77.159.208/oscilloscope.html).**

The first picture has been generated by bouncing off an end switch. ESP32 Oscilloscope performed digitalRead-s after the pin has been initialized in INPUT_PULLUP mode. The second picture shows noise coming from poorly regulated power supply. ESP32 Oscilloscope used analogRead-s on an unconnected pin.


![Screenshot](oscilloscope.png)


Esp32 oscilloscope takes approximately 40 samples per screen but the sampling rate may not be completely constant all the time since there are other processes, (beside the sampling process itself, especially if you are using Esp32 oscilloscope as a part of other projects) running at the same time. Esp32 oscilloscope displays the samples as they are taken which may not be exatly the signal as it is on its input GPIO. The samples are represented by digital values 0 and 1 or analog values from 0 to 4095 which corresponds to 0 V to 3.3 V.


Thanks to [gin66](https://github.com/BojanJurca/Esp32_oscilloscope/issues/19) you can even monitor signals on GPIOs that were configured for OUTPUT or PWM.



You are welcome to modify oscilloscope.html to match your needs, meaning, specify which GPIOs are actually used as digital inputs and which as analog inputs, to make some sense of what signals you are about to monitor.


ESP32 oscilloscope was first meant to be just a demonstration of the ESP32_web_ftp_telnet_server_template (https://github.com/BojanJurca/Esp32_web_ftp_telnet_server_template) capabilities and is still fully included there, but it seems to be
better off on its own. Only functionalities necessary for an oscilloscope to work are used here.


## Setup instructions

1. Copy all files in this package to the Esp32_oscilloscope directory.

2. Open Esp32_oscilloscope.ino with Arduino IDE.

3. Find and change YOUR-STA-SSID to your WiFi SSID and YOUR-STA-PASSWORD to your WiFi password.

4. Oscilloscope uses FAT file system so select one of FATFS partition schemas (Tools | Partition scheme | ...).

Some ESP32 boards do not have a flash disk. In this case just comment out the line #include "file_system.h" and ESP32 oscilloscope will use progmem instead of the file system to store the oscilloscope.html file.

5. Compile the sketch and run it on your ESP32 for the first time. Doing this, ESP32 flash memory will be formatted with the FAT file system. WARNING: every information you have stored into ESP32s flash memory will be lost.

6. FTP to your ESP32 (By using ftp command line utility or Windows explorer. User name and password are not required) and upload the following files into /var/www/html directory:

   - android-192-osc.png,
   - apple-180-osc.png,
   - oscilloscope.html.

```
C:\esp32_oscilloscope>ftp YOUR-ESP32-IP
Connected to 10.0.0.3.
220-ESP32 FTP server - everyone is allowed to login
User (10.0.0.3:(none)):
331 enter password
Password:
230 logged on, use "/" to refer to your home directory "/"
ftp> put android-192-osc.png /var/www/html/android-192.png
226 /var/www/html/android-192-osc.png transfer complete
ftp> put apple-180-osc.png /var/www/html/apple-180.png
226 /var/www/html/apple-180-osc.png transfer complete
ftp> put oscilloscope.html /var/www/html/oscilloscope.html
226 /var/www/html/oscilloscope.html transfer complete
ftp> ls
200 port ok
150 starting transfer
-r-xr-xrwx   1 owner    group            1818 Aug 24 10:47      /var/www/html/android-192-osc.png
-r-xr-xrwx   1 owner    group            1596 Aug 24 10:47      /var/www/html/apple-180-osc.png
-r-xr-xrwx   1 owner    group           39515 Aug 24 10:47      /var/www/html/oscilloscope.html
226 transfer complete
ftp>
```

6. Open http://YOUR-ESP32-IP/oscilloscope.html with your browser.


## Things to consider when analogReading GPIOs

ESP32 has two SARs (Successive Approximation Registers) built-in among which only ADC1 (GPIOs: 36, 37, 38, 39, 32, 33, 34, 35) can be used for oscilloscope analogReadings. ADC2 (GPIOs: 4, 0, 2, 15, 13, 12, 14, 27, 25, 26) can perform analogReadings only when WiFi is not working. Since oscilloscope uses WiFi, ADC2 GPIOs are not available at this time.