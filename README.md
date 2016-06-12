# ZathPanel

This is an evolving home automation control/status display panel. It's a Python program running on a [Raspberry Pi](https://www.raspberrypi.org) attached to an official [Raspberry Pi 7" Touchscreen](https://www.raspberrypi.org/products/raspberry-pi-touch-display/). It currently has three main sections:

1. On the left is an analog and digital clock (which, thanks to NTP is quite accurate and always properly set).
2. In the middle is the current weather conditions (combining readings from local sensors with data from Forecast.io and WeatherUnderground).
3. On the right is a scrollable list of buttons corresponding to lighting scenes for [Hue Lights](http://meethue.com) (color-coded for what effect they'll have on the lights) - touch one and the room lights change.

The UI is done using the [Kivy](https://kivy.org/) library, which handles the display and interactivity, though the only interaction currently is changing lighting scenes using the right-hand column.

The local weather data comes from a brilliant project called [WxShield](http://www.osengr.org/WxShield/Web/WxShield.shtml): it's an Arduino Uno equipped with a shield containing a very good barometer (a Bosch BMP085), a very good thermometer/hygrometer (a Sensirion SHT15 on the end of a 3ft cable so the board's warmth doesn't affect the temperature readings), and a 433MHz antenna and associated radio receiver hardware that can read and decode the signals sent by many different commercially available wireless remote weather sensors (from the likes of Oregon Scientific, AcuRite, Ambient Weather, etc). *Because smart people have reverse-engineered the necessary protocols*. Now, normally the WxShield talks to an open-source Windows/C# program called [Weather Station Data Logger (WSDL)](http://wmrx00.sourceforge.net), which does a substantial amount of decoding of the raw data coming in from the WxShield to end up with actual human-readable values. I have converted some of WSDL's lower level C# communication and conversion code into Python, and made a standalone program (called wxshield\_mqtt, which I'll put up soon), that collects the data from the WxShield hardware, decodes it, and publishes easily parsed values to an MQTT broker (running on another Raspberry Pi in-house), as new data comes in (several times a minute).

Another Python program (collect\_weather - I'm not very good with names) collects weather data from [Forecast.io](http://forecast.io) and [WeatherUnderground](https://www.wunderground.com) (via their official APIs), several times an hour, and publishes that data to the same MQTT broker. This allows me to have good forecast/precipitation info as well as, for instance, wind data without having to put an anemometer on the roof.

With the weather data all conveniently available from the MQTT broker ("one-stop shopping"), the front-end of this setup, ZathPanel *merely* subscribes via MQTT to the weather data updates and displays the data as it comes in (and handles talking to the Hue Bridge when I want to change the lights).

[![ZathPanel](http://carlrj.com/zathpanel/zathpanel.png)](http://carlrj.com/zathpanel)

## The name

The name came about because this is the primary function of the Raspberry Pi involved, which is named Zathras, after a character on Babylon V.

> "Cannot say. Saying, I would know. Do not know, so cannot say."  
> —[Zathras](https://en.wikipedia.org/wiki/Zathras), [Bablyon 5](https://en.wikipedia.org/wiki/Babylon_5): "[War Without End, Pt. 1](https://en.wikipedia.org/wiki/War_Without_End_(Babylon_5))"
>
>  (On being asked whether he _couldn't_ answer, or _wouldn't_ answer, a question.)

An enigmatic character if there ever was one. But an interesting provider of information. ZathPanel provides information.
