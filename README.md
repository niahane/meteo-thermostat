# Meteo-thermostat

This is the program I wrote with esphome for my aztouch electronic board

![aztouch_wall](https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/aztouch%20wall.jpg)
https://www.amazon.it/AZDelivery-AZ-Touch-custodie-pollici-ESP8266/dp/B081FC31Q5?th=1

the initial idea was to create a thermostat with weather forecast, not finding any project that I liked I decided to write it myself. Some existing projects involved the creation of multiple entities in the home assistant for example "day 1" forecasts, "day 2" forecasts etc ... creating a big mess in the home assistant. Then normally home assistant records by default each value of each sensor in its database, this thing is not very emmc/sd friendly, i know that exclusions can be made but I didn't feel like it.
So with the arduinojson library and with the help of a very capable C ++ friend we were able to unpack the forecast values directly on the esp without having to create any template in the home assistant.

IMPORTANT:
The last version of esphome uses arduinjson 6 so we had to reprogram all the json related section, make sure you have the latest version of esphome available otherwise you will get errors in compiling.

I added two sensors to the board:
A bme280 to have internal temperature, humidity and pressure.
![bme280](https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/bme280.jpg)

And a presence sensor to activate the backlight only on human presence.
![rclw](https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/rclw-0516.jpg)

![aztouch1](https://github.com/niahane/meteo-thermostat/blob/474219cd6f2cac05c4dfb25d911a39c119f23d16/readme_img/aztouch1.jpg)

The operation should be quite simple, you have to adapt your entities in the code especially the actuator which allows you to turn the boiler/heater on/off and the weather entity ID. In future versions I will use esphome substitutions to facilitate the insertion of these entities. 

It support touch screen and various home assistant sensors, the logic is all inside the esp32 so it should continue to work even in the absence of internet connection (the part concerning the thermostat) the desired temperature as well as the switching on / off of the thermostat can be entered from the touch screen or from the home assistant.
![hass](https://github.com/niahane/meteo-thermostat/blob/7e52d860cf970f4f9c97ee505d01e0b927ff10db/readme_img/hass_thermostat.jpg)

A demostation video:
https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/video.mp4?raw=true

I didn't want to put a 220v relay on the board so I used a 1v3 shelly by importing from home assistant the shelly entity to turn on and off .

On first run the thermostat had to be added as esphome integration in home assistant.
You have to change the secret.yaml file to adapt your wifi network

This is the section need to be adapted with your boiler/heater entity:
```
    heat_action:
      homeassistant.service:
        service: switch.turn_on
        data: {entity_id: switch.shelly_caldaia} <-Replace with your heater entity
    idle_action:
      homeassistant.service:
        service: switch.turn_off
        data: {entity_id: switch.shelly_caldaia} <-Replace with your heater entity
```
And this is only used to know the relay status:
```
  - platform: homeassistant
    name: "Caldaia"
    entity_id: switch.shelly_caldaia <-Replace with your heater entity
    id: sensor_caldaia
    internal: true
```    
You must also replace all weather entity in code matching your homeassistant weather entity:
```
sensor:
#Import external temperature
  - platform: homeassistant
    id: weather_temperature
    entity_id: weather.casa <-Replace this with your weather home assistant identity (probably met.no integration or openweathermaps)
    attribute: temperature
    internal: true
```
It supports also two power absorption optional entity.
They will be represented on the lower part of the display as icons with their value at the bottom.
```
#Total energy in watt
  - platform: homeassistant
    id: consumo_t
    entity_id: sensor.consumo_totale_power <-Change this with your total absorpion entity sensor
    internal: true
```    
#Shelly em washing machine absorpion in watt
```
  - platform: homeassistant
    id: consumo_c
    entity_id: sensor.consumo_cantina_power <-Change this with your partial absorpion entity sensor like washing machine, diskwasher etc...
    internal: true
```

I have created some C ++ dictionaries to help the user in translating days of the week, months and weather condition. Adapt them according to your language as well, an example:
```
         //Map condition//
         std::map<std::string, const char *> conDict;
         conDict["clear-night"] = "Notte serena";
         conDict["cloudy"] = "Nuvoloso";
         conDict["fog"] = "Nebbia";
         conDict["hail"] = "Nevischio";
         conDict["lightning"] = "Fulmini";
         conDict["lightning-rainy"] = "Fulmini/pioggia";
         conDict["partlycloudy"] = "Parz. Nuv.";
         conDict["pouring"] = "Rovescio";
         conDict["rainy"] = "Pioggia";
         conDict["snowy"] = "Neve";
         conDict["snowy-rainy"] = "Neve/pioggia";
         conDict["sunny"] = "Sereno";
         conDict["windy"] = "Vento";
         conDict["windy-variant"] = "Vento forte";
         conDict["exceptional"] = "Eccezionale";
         conDict[""] = "Sconosciuto";
```
With some adaptation the program can also work on other monitors:
![other_esp](https://github.com/niahane/meteo-thermostat/blob/ccecbf7e62d21f84fb1c07a17deb5a30e2a62981/readme_img/other-esp.jpg)

Once the firmware has been compiled and installed, the use from the touch screen is very simple. A tap on the central power button turns on the thermostat, to decrease / increase the desired temperature use the touch keys + and -
If the motion sensor sees something, the + and - buttons become two eyes for a few seconds.
To turn off the thermostat, just press and hold the icon with the shape of a fire.
If the boiler / heater is on the flame will turn red otherwise it will be gray.
To avoid problems with the boiler, there is a minimum time in which it will be on, you will sometimes notice that by turning off the thermostat or decreasing the desired temperature, the boiler will remain on for a few seconds, this is intentional it prevents too many consecutive starts and stops which could damage it..

I worked a lot on this program, it was also a way to learn some basic things about lambda (c ++) I hope you like it and that its code will help you in your projects. I'd love some feedback or suggestions from you, so keep me updated if you add more features! 
I'm new to github I don't know if the "issue" section already works, if doesn't, please write to me at meconiotech@gmail.com
Greetings from Niahane
