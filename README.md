# Meteo-thermostat

This is the program I wrote with esphome for my aztouch electronic board

https://www.amazon.it/AZDelivery-AZ-Touch-custodie-pollici-ESP8266/dp/B081FC31Q5?th=1

I added two sensors to the board, a bme280 to have temperature, humidity and pressure.
![bme280](https://github.com/niahane/meteo-thermostat/blob/7a2923ba1b6a4c6382c3172988ddbd63d4416278/readme_img/bme280.jpg)

And a presence sensor to activate the backlight of the monitor so as not to be always turned on.

The operation should be quite simple, you have to adapt your entities in the code especially the actuator which allows you to turn the boiler and the weather entity on and off. In future versions I will use overrides to facilitate the insertion of these entities.
