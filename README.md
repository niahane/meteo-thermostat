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

The operation should be quite simple, you have to adapt your entities in the code especially the actuator which allows you to turn the boiler/heater on/off and the weather entity ID.

It support touch screen and various home assistant sensors, the logic is all inside the esp32 so it should continue to work even in the absence of internet connection (the part concerning the thermostat) the desired temperature as well as the switching on / off of the thermostat can be entered from the touch screen or from the home assistant.
![hass](https://github.com/niahane/meteo-thermostat/blob/7e52d860cf970f4f9c97ee505d01e0b927ff10db/readme_img/hass_thermostat.jpg)

A demostation video:
https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/video.mp4?raw=true


I added the substitutions section fit entities with yours:

Heater is the entity can be called from esp to turn on/on the heater/boiler in my case a shelly 1v3 because I didn't want to put a 220v relay on the board so I used a 1v3 shelly as external relay and calling in climate section the home assistant service for turn on/off the relay.

Weather is the homeassistant weather entity for extracting weather conditions, forecasts... In my case "weather.casa"

This program supports two optional power absorption sensors (tc is "total cosumpion", pc is "partial consumption") which will represent at the bottom of the screen as two icons with their watt values below. 
For the partial consumption sensor a binary sensor is also needed to know the on / off status of the device
see the yaml file code comments.
If you do not have them you can also not change anything, they appear only if the sensor exists.
```
# Substitutions
substitutions:
# Heater entity from home assistant
  heater: switch.shelly_caldaia <-must be replaced with your heater entity
# Weather entity from home assistant
  weather_entity: weather.casa <- must be replaced with your weather entity
# Weather now icon size
  icon_xy: '65x65'
# Total consumption sensor to import from home assistant
  tc: sensor.consumo_totale_power <- must be replaced with your total absorpion sensor entity (optional)
# Partial consumtion entity like washing machine etc to import from home assistant
  pc: sensor.consumo_cantina_power <- must be replaced with your partial absorpion sensor entity (optional)
```
Before try to compile the firmware you have to change the secret.yaml file to adapt your wifi network
On first run the thermostat had to be added as esphome integration in home assistant.

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

This board also has a buzzer which can be used in home assistant. For example, I use it as an alarm siren by calling this service in an automation if the door is opened while the alarm is armed:
```
   service: esphome.termostato_meteo_play_rtttl
    data:
      song_str: siren:d=8,o=5,b=100:d,e,d,e,d,e,d,e
```

Once the firmware has been compiled and installed, the use from the touch screen is very simple. A tap on the central power button turns on the thermostat, to decrease / increase the desired temperature use the touch keys + and -
If the motion sensor sees something, the + and - buttons become two eyes for a few seconds.
To turn off the thermostat, just press and hold the icon with the shape of a fire.
If the boiler / heater is on the flame will turn red otherwise it will be gray.

Important:
To avoid problems with the boilers, there is a minimum time in which it will be on, you will sometimes notice that by turning off the thermostat or decreasing the desired temperature, the boiler will remain on for a few seconds, this is intentional it prevents too many consecutive starts and stops which could damage it..

I worked a lot on this program, it was also a way to learn some basic things about lambda (c ++) I hope you like it and that its code will help you in your projects. I'd love some feedback or suggestions from you, so keep me updated if you add more features! 
I'm new to github I don't know if the "issue" section already works, if doesn't, please write to me at meconiotech@gmail.com
Greetings from Niahane
