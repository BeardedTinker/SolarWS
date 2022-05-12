# ESPHome code for Solar Weather station by [Open Green Energy](https://github.com/deba168) - Debasish Dutta 

You will be able to find here file(s) used to power Solar Powered WiFi Weather Station V3.2 by [OpenGreenEnergy](https://github.com/deba168).

While most of the functionality has been kept for this ESPHome version, there are currently some limitations and one NO-GO (UV sensor).

YouTube video of the project is on [this link](https://youtu.be/oHFcxun0XlA).

I really would like to thank Debasish Dutta for creating this project - multiple versions and for sharing it with community!

## Original project links
Some of the links for that project from author are:
- [Version 2](https://www.instructables.com/Solar-Powered-WiFi-Weather-Station-V20) - previous version
- [Version 3](https://www.instructables.com/Solar-Powered-WiFi-Weather-Station-V30/) - current/latest version

## PCB and assembled PCB links
Links to ordering PCB's and pre-assembled PCB's from PCBWay:
- [PCB only](https://www.pcbway.com/project/shareproject/Solar_Powered_WiFi_Weather_Station_V3_0.html), you have to supply all components
- [Assembled PCB](https://www.pcbway.com/project/gifts_detail/Solar_Powered_WiFi_Weather_Station_V3_0.html) with ESP32 and solar panel, other components are extra

## Links to additional componets (if you order assembled PCB these are also needed)
Links to additional components needed for the project that I ordered:
- Temperature sensor [DS18B20](https://www.aliexpress.com/item/4000895660165.html)
- Temperature, humidity & pressure sensor [BME280](https://www.aliexpress.com/item/1005003622447376.html)
  (or alternative - [BMP280](https://s.click.aliexpress.com/e/_At5nFR) - without humidity sensor)
- Lux sensor [BH1750](https://s.click.aliexpress.com/e/_AazuEN)
- Rain bucket [MS-WH-SP-RG](https://s.click.aliexpress.com/e/_A4Jxal)
- Wind speed sensor [WH-SP-WS01](https://s.click.aliexpress.com/e/_ACndMN) 
- Wind direction sensor [MS-WH-SP-WD](https://s.click.aliexpress.com/e/_9IxMIh)
- 18650 battery [LittoKala NCR18650B 3.7V 3400mAh](https://s.click.aliexpress.com/e/_AM6kqp)

## Weather station housing
[Link to STL](https://www.thingiverse.com/thing:4805867) files for weather station. Don't use PLA! I've printed everything with ABS.

# About the code

Code is my attempt to convert what original author has done to ESPHome.
There are still some things that need to be fixed, checked and calibrated, but since this supports OTA updates even with deep sleep, this shouldn't be the problem to implement or improve in future.

MQTT? Sure, it can work as good with MQTT as it is working with Home Assistant API, BUT I deceided to go API route.
At this point, I'm not sure when or if I'll make MQTT version of the code or maybe even dual version in same file.

I've included sample.log that is dump of one cyle (awake cycle) from weather station. So far these are values I get. At the time of recording of this log, no wind or rain was detected.

## Deep sleep
Device goes into deep sleep after 30s of being online. This value can be customized (reduced or increased if needed). 
Sleep lasts for 5 minutes (also can be changed if needed).

So far in about 1 month of testing, solar panel charging was enough to keep device working.

In case there is problem with inadequate charging, there are two options on how to improve this:
- reduce awake and increase sleep time. My default data collection time for LaCross weather station was 12 minutes (5 data points in hour)
- replace solar panel with larger one

## OTA
As device should be located outside, I've added sleep prevention in ESPHome code, plus switch in Home Assistant.
If you need to push update, you just flip the switch, and next time edvice awakes and connects it will remain awake.

At that time you can push wireless OTA via ESPHome. When update ends and is successful, switch will be flipped back to off automatically and device will go to sleep.

## Battery tracking

For battery tracking, ADC platform in ESPHome is used. It is not perfect as values can fluctuate.
Also, for now (needs fix) % of battery can be missed every second data report to HA.

To get best results, you should record maximum Voltage reported by device when battery is fully charged, and also, write down last value device reported before it fell ofline due to empty battery.
These values are then used to track 0-100% state of the battery.

## BME280

BME sensor can be replaced with BMP sensor, but you will lose humidity % data. If you want to use it, also in code, part of it has to be commented out:
```
    humidity:
      name: '${display_name} Humidity'
      oversampling: 4x
      unit_of_measurement: "%"
      icon: "mdi:thermometer"
      device_class: "humidity"
      state_class: "measurement"
```

For BME and BMP, you have to enable `i2c` in ESPHome code - enabled by default.

## Dallas DS18B20

In order to add Dallas as additional (external) temperature sensor, you have to do 2 steps.
In first step, you need to enable it with following code (already included):
```
dallas:
  - pin: GPIO4
```
After reboot/flash, you wil in log files see address for Dallas sensor, and you than have to updated it in the code:
```
# DS18B20 Dallas temperature sensor
  - platform: dallas
    address: 0xc43c01f096e8b728
    name: '${display_name} Air Temperature'
    unit_of_measurement: "°C"
    icon: "mdi:thermometer"
    device_class: "temperature"
    state_class: "measurement"
    accuracy_decimals: 1    
```
## Rain sensor

Rain sensor is working as simple pulse counter. This should already be tweaked to work with standard rain bucket/sensor.

## BH1750 lux sensor

Lux sensor is implemented in the code and is using standard `i2c` address.

## Wind speed sensor

Wind speed sensor is also using pulse meter. Downside of course is taht as it is looking at 5 second interval (currently), if wind is very slow, you could miss data here.
This code will also be tweaked if/when I get handheld anemometer to compare values with ones from the sensor.

## Wind direction sensor

While adding sensor to the weather station is not hard, it may require you to calibrate it to your version of sensor.
Easiest way it to position sensor to each of the positions by hand (N, NE, E, SE, S, SW, W, NW) and read resistance value from the sensor.
Thank later just expand the range for each of cardial directions until you get constant readings.

# Adding to Home Assistant

Device should automatically be detected in Home Assistant.
But in order to make OTA easier, you still need to create one Helper or Input Boolean inside home assistant.

`input_boolean.solarws_prevent_deep_sleep`
I use following yaml for that:
```solarws_prevent_deep_sleep:
  name: Prevent Solar WS Deep Sleep
  icon: mdi:sleep-off
  initial: false
  ```

This will enable you to toogle Deep Sleep of when needed to push OTA.

# Future

For me, this device will be located off site, out of reach in location with plenty of sun.
I will try to update code, improve efficiency, calibrate what I can and keep ESPHome version of the project alive.
This device will be permanent replacement of LaCross 2300 that has been working for more than 15 years now.

## Issues

* Not sure is wind speed is caluclated correctly. One roration is 2 pusles. Data is collected in 25 second window. Have to recheck when I get handheld anemometer it values are correct
~~* Every other cycle, battery voltage % is not reported. Not sure why this happens. Forcing now update every 25 seconds to ensure some value is sent (FIXED)~~
* Can't use or figure how to use formula in battery % sensor:
`return ((id(voltage).state - ${battery_empty}) / ${battery_diff} * 100.00);`
* Sometimes Inpute Boolean in HA is not triggered to OFF after OTA. If you push another update while still awake, then switch is flipped. Maybe adding a bit of delay could help?


## To-Do

* At this point, ESPHome doesn't support UV sensor, so until this sensor is added in ESPHome to work out of box, there is no code that supports it
* Rain bucket doesn't count rain while in deep sleep. There is a workaround but this still isn't implemented
~~* Wind speed sensor doesn't detect wind while in deep sleep. There is also workaround on this but this could seriously decrease battery life - DONE~~
