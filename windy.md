# Windy Weather Gizmo

Weather gizmo should mainly track:
1. Temperature
2. Pressure
3. Humidity
4. Wind Speed
5. Wind direction

I think a proto-weather tracker could just track 1, 2, and 3 especially since the BME280 sensor tracks all three out of the box.

But I really think a true MVP would include wind speed and direction.

# Wind Speed and Direction

Speed and direction can be computed using a rotating magnet with N/S polarity on the perimeter of a rotating shaft.

As a top view and the shafting going out of the page (the z axis, so to speak):
   
    [N⨁S]

A magnetic hall sensor like AS5600-ASOM can track absolute position of the N-S magnet direction when it's directly below the magnet. This could be used for direction.

## Wind Speed
For speed wind speed, there are a couple of options. A wind speed sensor is called a [Anemometer](https://en.wikipedia.org/wiki/Anemometer). 
The classic one is the 3 cups separated 120 degrees. There's some interesting physics in computing the spin speed as a function of the wind speed. 
This generally reduces to just a coefficient between 0 and 1 multiple of the real wind speed, and is generally recommended to be measured/callibrated
empirically, although the general form of the meter can give a rough indication of what the coefficient would be. The difficulty is that to calibrate
the speed we need an independent measure of the wind speed. 🤷

A wind 3 or 4 cup anemometer could be measured with the same magnet as above, except hall latch sensors could be placed in the xy-plane of the magnetic poles.
The hall latch sensors switch on and off with the passing of the North and South poles by the sensor. This can be detected and the spinning speed can be determined
and then the wind speed can be computed that way.

## Force sensor
Another way to measure the speed is to use a force sensor. Wind will impart a pressure on a flat plate. If the plate is normal to the wind then it's easy to
compute the pressure which is dependent on wind speed and the plate area. The total force is just the pressure multiplied by the plate area, and the pressure
sensor changes resistance based on the force. So the wind speed could be calculated in this manner if a plate could be placed normal to wind direction at all
times. This has the advantage that the only calibration needed is for the force sensor itself and how the resistance relates to force. But is perhaps harder to
construct in a way to maintain a plate normal to the wind? I'm not sure.

# Getting data out
Not sure the best way to get data out of the instrument. The instrument could constantly be supplying several time series data in memory, and then give it up
when asked. Or perhaps it just has the latest measurement at the ready for polling, or maybe it only performs the measurement when polled.

Is there a model where it's not polled, but pushes data to a host?

The device should not be directly connected to the internet, as Commander Adama wisely tells education secratary Laura Roslin on the Battlestar Galactica.
