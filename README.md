# ESPHome code for Solar Weather station by [Open Green Energy](https://github.com/deba168) - Debasish Dutta 

You will be able to find here file(s) used to power Solar Powered WiFi Weather Station V3.0 by [OpenGreenEnergy](https://github.com/deba168).

While most of the functionality has been kept for this ESPHome version, there are currently some limitations and one NO-GO.

Project is still been worked on and while planned release of the code and video is soon(tm), there will always be place for improvement

I really would like to thank Debasish Dutta for creating this project - multiple versions and for sharing it with community!

## Original project links
Some of the links for that project from author are:
- [Version 2](https://www.instructables.com/Solar-Powered-WiFi-Weather-Station-V20)
- [Version 3](https://www.instructables.com/Solar-Powered-WiFi-Weather-Station-V30/)

## PCB and assembled PCB links
Links to ordering PCB's and pre-assembled PCB's from PCBWay:
- [PCB only](https://www.pcbway.com/project/shareproject/Solar_Powered_WiFi_Weather_Station_V3_0.html), you have to supply all components
- [Assembled PCB](https://www.pcbway.com/project/gifts_detail/Solar_Powered_WiFi_Weather_Station_V3_0.html) with ESP32 and solar panel, other components are extra

## Links to additional componets (if you order assembled PCB these are also needed)
Links to additional components needed:
- Temperature sensor (DS18B20):
- Temperature, humidity & pressure sensor (BME280):
- Lux sensor ()
- Rain bucket
- Wind speed sensor
- Wind direction sensor
- 18650 battery

## Weather station housing
[Link to STL](https://www.thingiverse.com/thing:4805867) files for weather station. Don't use PLA! I've printed everything with ABS.

# About the code

Code ismy attempt to convers what original author has done to ESPHome.
There are still some things that need to be fixed, checked and calibrated, but since this supports OTA updates even with deep sleep, this shouldn't be the problem to implement or improve in future.

## To-Do

* At this point, ESPHome doesn't support UV sensor, so until this sensor is added in ESPHome to work out of box, there is no code that supports it
* Rain bucket doesn't count rain while in deep sleep. There is a workaround but this still isn't implemented
* Wind speed sensor doesn't detect wind while in deep sleep. There is also workaround on this but this could seriously decrease battery life
