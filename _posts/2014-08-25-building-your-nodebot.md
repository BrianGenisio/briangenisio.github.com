---
layout: post
title: "Building Your NodeBot"
description: "How to build the reference NodeBot"
category: Software
tags: [NodeBots, Arduino, Johnny-Five]
---

For our ["Battle of the NodeBots" Hackathon project](/software/2014/08/23/rise-of-the-nodebots.html), we will be building a bot to compete against other bots.  We have a kit which includes the following components.  

- (1) [SainSmart Uno Arduino Clone](http://www.sainsmart.com/sainsmart-uno-r3-atmega328-au-development-board-compatible-with-arduino-uno-r3.html)
- 5mm Wood Platform -- (1) 3" x 6.75" panel and (2) 2.5" wheels
- (2) Parallax [Continuous Rotation Servos](http://www.parallax.com/product/900-00008)
- (1) [5/8" Ball Caster](http://www.amazon.com/TruePower-Roller-Ball-Transfer-Bearings/dp/B009KAQVWC/ref=sr_1_4)
- (1) [Small Breadboard](http://www.amazon.com/SYB-170-Color-Board-Small-Bread/dp/B00ARUF2JM/ref=sr_1_2)
- (6) [Solderless Breadboard Wires](http://www.amazon.com/Wosang-Solderless-Flexible-Breadboard-Jumper/dp/B005TZJ0AM/ref=pd_bxgy_e_img_y)
- (2) [3-pin Headers](http://www.amazon.com/5pcs-Angle-Single-Header-Strip/dp/B0079SK5MO/ref=sr_1_4)
- (1) 10' USB Cable (A to B)
- (1) 9v Battery
- (1) 9v to 2.1mm Battery Clip
- (4) Zipties
- (2) 1" Double-Stick Tape
- (6) #6x3/8" Self-Drilling Screws

You are encouraged to build your bot however you want, but this is how you can build it without any modifications from the original design.

### Lay out your parts
You want to get a feel for where everything will go.  Lay your major components out on the board and rough-in where you think you'd like them to go.
![Rough-out the parts](/images/nodebot-assemble/nodebot-rough-out.jpg)

### Attach your wheels to your servos
The easiest way to do this is to remove the servo horn and attach the horn to the wheels using the self-drilling screws.  The holes in the servo horn are smaller than the screws but these things make quick work of the plastic and go right through to the wood.  Just make sure they are centered. Reattach the horns when you are done.
![Attach the wheels to the servo horns](/images/nodebot-assemble/nodebot-attach-wheels-to-servo.jpg)

### Attach the servos to your chassis
You can do this however you want.  A quick-and-dirty approach is to use double-sided tape.  I prefer zip-ties.  Figure out where you want the servos to sit and mark it on the board.  Use a drill to punch a hole through the wood.  Attach the servos to the wood using zip-ties and snip off the excess zip-tie.

![Mark your servo location](/images/nodebot-assemble/nodebot-mark-servo-location.jpg)
![Attach the servos with zip-ties](/images/nodebot-assemble/nodebot-attach-servos.jpg)

### Attach the caster wheel to the bottom of the chassis
The self-drilling screws are really useful for this.
![Attach the front caster wheel](/images/nodebot-assemble/nodebot-attach-caster2.jpg)

### Attach the Arduino and Breadboard
Affix the Arduino using double-stick tape.  Affix the breadboard using the attached mounting tape.
![Affix the boards](/images/nodebot-assemble/nodebot-attach-boards.jpg)

### Mount the battery
Using double-stick tape, affix the battery to the bot.  Connect it to the Arduino using the 9v clip.
![Affix the battery](/images/nodebot-assemble/nodebot-attach-battery.jpg)

### Power the servos from the board
You are not likely to keep the servos here permanently, but this is a good starting point.  Pins 9 and 10 support PWM signals, which are best for servos.  You can connect them to the SVG pin headers for now.  The white leads are the "signal" for the SVG pins.

![Power the servos](/images/nodebot-assemble/nodebot-plug-servos.jpg)

Your servos may start spinning now.  If that happens, you can trim them using the screws on the back of the servos.  You may need to do this again once you have hooked your system up to the software.

![Trim the servos](/images/nodebot-assemble/nodebot-calibrate.jpg)

### Start programming your bot!

Now you have a working bot.  You can plug your USB cable into the board and go to town.  If you haven't already [gotten yourself bootstrapped](/software/2014/08/25/bootstrapping-your-arduino-with-firmata-and-johnny-five.html), now is a good time.

Take a look at the [servo API](https://github.com/rwaldron/johnny-five/wiki/Servo) in Johnny-Five to get you started.  Here is a [simple example](https://github.com/rwaldron/johnny-five/blob/master/docs/servo-continuous.md) that will rotate your 10-pin servo clockwise.

![Start Programming](/images/nodebot-assemble/nodebot-program-bot.jpg)




