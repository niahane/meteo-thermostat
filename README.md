# Meteo-thermostat

This is the program I wrote with esphome for my aztouch electronic board
![aztouch_wall](https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/aztouch%20wall.jpg)

https://www.amazon.it/AZDelivery-AZ-Touch-custodie-pollici-ESP8266/dp/B081FC31Q5?th=1

I added two sensors to the board, a bme280 to have internal temperature, humidity and pressure.
![bme280](https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/bme280.jpg)

And a presence sensor to activate the backlight of the monitor so as not to be always turned on.
![bme280](https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/rclw-0516.jpg)

The operation should be quite simple, you have to adapt your entities in the code especially the actuator which allows you to turn the boiler and the weather entity on and off. In future versions I will use esphome substitutions to facilitate the insertion of these entities. 

It support touch screen and various home assistant sensors, no template is needed in the home assistant the operating logic is all inside the esp32 so it should continue to work even in the absence of internet connection (the part concerning the thermostat) the desired temperature as well as the switching on / off of the thermostat can be entered from the touch screen or from the home assistant.
![hass](https://github.com/niahane/meteo-thermostat/blob/7e52d860cf970f4f9c97ee505d01e0b927ff10db/readme_img/hass_thermostat.jpg)

I didn't want to put a 220v relay on the board so I used a 1v3 shelly by importing from home assistant the shelly entity to turn on and off .

A demostation video:
https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/video.mp4?raw=true

On first run the thermostat hadd to be added as esphome integration in home assistant
