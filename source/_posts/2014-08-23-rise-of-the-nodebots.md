---
layout: post
title: "Rise of the NodeBots"
description: "Creating NodeBots for a Hackathon"
category: Software
tags: [NodeBots, Arduino, Johnny-Five]
permalink: software/2014/08/23/rise-of-the-nodebots.html
---

Every 8 weeks at [our company](http://letsfixhealthcare.com), we take two days off to hack on something new.  It is a chance to try new ideas, learn new technologies, or exercise our brain in ways we don't tend to do in our everyday roles.  These "Hackathons" are fully supported by the owner of our company as he believes these periodic shifting of gears are beneficial to our creativity and to our product portfolio.

We don't always produce something useful.  Sometimes we learn that a given technology or approach is not for us.  Sometimes, however, we take what we learn and apply the ideas directly to our product.  Dozens of features and even full products have their roots in our "Hackathons".  They have proven to be exceptionally beneficial for the culture of our workplace.

### Trying Something New -- NodeBots
![Nodebot Army](/images/nodebot-army.JPG)

At our next Hackathon, we are organizing an event: "Battle of the NodeBots".  Approximately 20 of us will be building robots that will fight in a ring for supremacy.  Everyone will be given the same base kit.  If any of us build the kit like the reference bot, it is very unlikely we will win -- those with beneficial innovations are likely to have the advantage.

Innovations will come in one of three forms:

- **Software:** Pre-programmed maneuvers, articulated control, sensing systems, etc will help the bot gain an edge in the "brains" department.  Most people will be using Node and [Jonhhy-Five](https://github.com/rwaldron/johnny-five) to program their bots, though nothing is stopping anyone from writing C code that runs on the metal.
- **Electronics:** Extra servos, motors, controllers, or sensors will help the bot gain an edge in the "motion and sensing" department. This is where most of us will be learning the most.  We are software developers, not electronics engineers!  We will have a library of electronics components to pull from.
- **Mechanical:** Wheel traction, caster location, weight, size, articulated parts, etc will gain an edge in the "physical" department.  We will have a library of building supplies to pull from.

### The Kit
![Nodebot Army](/images/nodebot-kit.JPG)

Inspired by the [SumoBot Jr.](http://sumobotkit.com/) and the [SimpleBot](https://github.com/nodebotsau/SimpleBot), I was able to put together a kit that came in around $45 per bag.  This is the kit we that everyone will get:

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

Here is the bot fully assembled:
![Nodebot Army](/images/nodebot-assembled.JPG)

### The Lab
In order to enable everyone to be creative, we will have a stocked lab:
- Electronics (Resistors, Capacitors, Diodes, Transistors, Wires, Headers, etc)
- Displays (LEDs, LED Arrays, 8-Segments, Tri-color LEDs, LCDs, etc)
- Locomotion (Servos, Motors, Gears, Propellers, etc)
- Sensors (Light, Proximity, Temperature, Water, etc)
- Control (Switches, Joysticks, Keypads, Potentiometers)
- Building (Soldering Iron, Hot Glue Gun, Drills, Saws, Voltmeters, Screw Drivers, Pliers, etc)
- Crafts (Wood, Popsicle Sticks, Dowel Rods, Wire, Flashing, Screws, etc)

### The Setup
We are going to use [Jonhhy-Five](https://github.com/rwaldron/johnny-five) to control our bots.  This mechanism uses serial (USB) communications to talk to our Arduino via the [Firmata](http://firmata.org/wiki/Main_Page) protocol.  This will require everyone to install [StandardFirmata](https://github.com/firmata/arduino/blob/master/examples/StandardFirmata/StandardFirmata.ino) on the board.  We will have a couple of these [Bluetooth to Serial adapters](http://www.amazon.com/JY-MCU-Arduino-Bluetooth-Wireless-Serial/dp/B009DZQ4MG/ref=sr_1_1) available if anyone feels like spending their valuable time [getting it working ](https://github.com/rwaldron/johnny-five/wiki/JY-MCU-Bluetooth-Serial-Port-Module-Notes).

### More to Come!
I plan to document more about this event as I go.  We'll see how it goes.  I'll be [tweeting ](http://twitter.com/BrianGenisio) photos and recording video of the battles.  Stay tuned!