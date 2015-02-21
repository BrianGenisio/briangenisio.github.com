---
layout: post
title: "Piece of Mind Temperature Monitor"
description: "How I eased my mind in the bitter cold"
category: Software
tags: [Spark Core, Hacking, IOT, Internet of Things]
---

![Siesta Key](/images/siesta_key.jpg)

I just got home from 5-day vacation in [Siesta Key](http://www.drbeach.org/top10beaches.htm) where my family and I frolicked in the the slightly warmer climate than what we are used to in Ann Arbor Michigan.  The entire eastern half of the nation was under a [polar vortex](http://en.wikipedia.org/wiki/Polar_vortex) so our balmy Floridian host was much cooler than expected.  Back home, however, the temperature dropped as low as -28 degrees Fahrenheit (-33 degrees Celsius).

This wouldn't normally cause me any anxiety if it were not for my 22 year old furnace.  I couldn't help but worry that it would fail, causing my pipes to freeze followed by all sorts of damage.  I knew I wouldn't be comfortable leaving my home without feeling confident that my furnace was keeping up.

## Spark Core + Thermistor
I dove into my basement to scrounge for parts and I built myself an "Internet of Things" thing to monitor the temperature inside my home.  It was really easy and it only took me 30 minutes.  I had an unused [Spark Core](https://www.spark.io/) and a [Tinkerkit Thermistor](http://www.amazon.com/Arduino-TinkerKit-Thermistor-Module/dp/B00EIB48W8) lying around, so I used them.  Any thermistor would have worked, but that is the one I found in my lab.

![Spark + Thermistor](/images/spark_temperature.jpg)

I hooked up ground, power, and signal (to Analog 7) and got coding.  I found some conversion code for this Tinkerkit module on the [Johnny-Five](https://github.com/rwaldron/johnny-five) project, and I ported it over to C.  The Spark.io builder made it easy for me to send the following code to my Spark Core:

![Spark + Thermistor Diagram](/images/spark_temperature_fritz.png)

```c
#include "math.h"

double temperature = 0.0;

const double RESISTOR = 10000;
const double ADCRES = 4095;
const double BETA = 3950;
const double GINF = 120.6685;
const double KELVIN = 273.15;

void setup() {
    Spark.variable("temperature", &temperature, DOUBLE);
    pinMode(A7, INPUT);
}

void loop() {
  int raw = analogRead(A7);
  double rthermistor = RESISTOR * (ADCRES / raw - 1.0);
  double tempC = BETA / (log(rthermistor * GINF));

  temperature = tempC - KELVIN;
  temperature = (temperature * 9.0) / 5.0 + 32.0; // to F
}
```

That was it.  From there, the "temperature" variable was exposed to the web.  The value was available from a simple URL: `https://api.spark.io/v1/devices/MY_DEVICE_ID/temperature?access_token=MY_ACCESS_TOKEN`

```
{
  cmd: "VarReturn",
  name: "temperature",
  result: 67.39388212240802,
  coreInfo: {
    last_app: "",
    last_heard: "2015-02-21T01:59:44.665Z",
    connected: true,
    deviceID: "MY_DEVICE_ID"
  }
}
```

Cool (or warm?)!  But I don't really feel like reading JSON on my vacation.

## CodePen to Host a Simple UI
I didn't want to go through the hassle of creating a tiny site and publishing it just to read this value.  This is why I like [CodePen](http://codepen.io) and their ilk.  I can write up a quick little code snippet (note that I'm using ES6), and I'm good to go.

```html
<h1>The temperature at home is <span id="temperature">...</span></h1>
<button id="reload" class="btn btn-primary">Reload</button>
```

```js
function getTemperature() {
  var device = "MY_DEVICE_ID";
  var key = "MY_ACCESS_TOKEN";
  var url = `https://api.spark.io/v1/devices/${device}/temperature?access_token=${key}`;
  $.getJSON(url, json => $('#temperature').text(json.result.toFixed(1) + " degrees"));
}

$(function() {
  getTemperature();

  $('#reload').on('click', function() {
    $('#temperature').text('...');
    getTemperature();
  });
});
```

Now I can go to the "full-page" view of the site from my phone.  The UI was only hacked together so it is crude (remember, this entire project only took 30 minutes to hack together), but it works!

![See the temperature](http://i.imgur.com/wrm0jHJ.png)

But I really don't want to be checking my phone every 5 minutes.

## Setting Up Phone Alerts

I really like [IFTTT](http://ifttt.com).  It stands for "If This Then That".  I published a [recipe](https://ifttt.com/recipes/261514-cold-house) that says: "If [the temperature goes below 55 degrees] then [send an alert to my phone]".

I had to put the device in my freezer, but I received an alert on my phone that the temperature dropped below 55 degrees!

![A phone alert](/images/ifttt_alert.png)

## Did My Furnace Fail?
No, it did not.  BUT, I was never worried about it on vacation because I always knew that it was holding up.  In fact, on the last day of our vacation, the temperature jumped from 65 degrees to 70 degrees.  At first I was confused, but then I learned that our house cleaners had come that morning and they bumped the temperature up in anticipation of our arrival home!  

This is why I love living in the future.  We have some amazing abstractions here.  I can focus on the details that matter to me and I let the infrastructure do the rest for me.  This is the kind of thing we'll be seeing more and more of going forward as the "Internet of Things" becomes more ubiquitous.
