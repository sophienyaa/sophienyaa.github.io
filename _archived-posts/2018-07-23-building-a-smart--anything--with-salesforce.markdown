---
layout: post
title: Building a smart ‘anything’ with salesforce
date: 2018-07-23

---
This blog post is an expanded version of a talk I gave at the July 2018 [London Salesforce Developer meetup](https://www.meetup.com/LondonSalesforceDevelopers/events/249774155/) with slides available [here](https://docs.google.com/presentation/d/1tEnm6SBiYyBc1--nVwbmrS1jRSOE_glh1goHcLwqhrM/edit?usp=sharing)

I am sure by now we’ve all heard of the ‘internet of things’ and maybe someone us even have some ‘smart devices’ in our homes or offices. Maybe you’ve heard of Salesforce IoT. This post will show you the things you need to build a smart ‘anything’ on salesforce.

Before we go too much further, we need to understand event driven architecture, which is the underpinnings of many IoT (and general) integrations. What I mean by event driven architecture is this; Rather than a standard point to point (one system connects directly to another to exchange data) or middleware (there is software in the middle of the systems to manage the exchange of data) integration, event driven integrations rely on three things. These are publishers, subscribers and the event bus. Simply put, systems or devices publish or subscribe to messages to send and receive data, and the event bus manages this. A major advantage of this approach is that systems or devices are decoupled from one another, so if things need to change or a system needs replacing entirely, the other systems are not affected by this change.

![](/img/0*d8uWeZZe-4rWbQIV.png)

Now that we understand event driven architecture (hopefully, here is a good [trailhead](https://trailhead.salesforce.com/en/modules/platform_events_basics/units/platform_events_architecture) for more info), on to our smart ‘anything’, in this case its a smart powerstrip. I chose this example because it is easy to demonstrate and relate to, as many people have smart devices in their homes or businesses. The concepts here are not limited to domestic use, this could just as easily be applied to a smart refrigerator, projector, water tank, conveyor belt, etc. 

Let's work out how we are going to make our smart power strip. We will need some hardware in the form of relays, sensors and microcontrollers (more on this in a minute) and we will need an event bus, so say hello to platform events (and MQTT), we will also need some automation and a user interface, so enter Salesforce IoT and Lightning respectively.

Starting with the hardware, inside of this fetching white box we have the following items

1. A modified two gang powerpoint (modified so that each outlet can be controlled individually)
2. Two cheap 5v relay boards from amazon
3. A current sensor (ACS712A, 5 amp version)
4. An Arduino nano
5. An NodeMCU 1.0 ESP8266 Dev Board
6. A 240VAC to 5VDC power supply (aka. sacrificial phone charger)
   The hardware is a tad more complex than necessary because I used what I had on hand to build this device. The relay boards and the current sensor both are designed to operate on 5v, where as the ESP8266 is a 3.3v device. So I am essentially using the Arduino nano to control all of the sensors and relays, and the ESP8266 is acting as a glorified serial to wifi converter. If I were to build this again I would do away with the Arduino Nano, as the ESP8266 does have everything needed to interface with sensors and relays directly.

![](/img/0*ktNFyA80UPvYzUOA.png)

The code on the arduino nano is simple, every 5 seconds it prints the current draw (in watts) to the serial port, It also listens for some JSON from the ESP8266 to tell it which pin and what value to set it to, to turn the relays on or off. You can check this code out on my github [here](https://github.com/mickwheelz/salesforce-iot-demo/blob/master/watts-json.ino)

The ESP8266’s code is a bit more complex, it acts as an interface between the Arduino Nano and the outside world, so on an emulated serial port it listens for the current draw information and sends it via MQTT and Platform Events in to salesforce. It also listens for a Platform Event telling it which device to turn on or off. You can check out the code for it [here](https://github.com/mickwheelz/salesforce-iot-demo/blob/master/mqtt-salesforce-demo.ino).

![](/img/0*bQ8nCwNXdfAifNt8.png)

So now we need a way for our hardware to talk to our event bus, while it is possible for it to talk directly to salesforce, as I discovered this isn’t a great idea (more on that later). I built a very quick and dirty ‘proxy’ between Platform Events and MQTT (MQTT is another Event Driven protocol, very similar to platform events but designed for low power applications). This proxy is written in NodeJS and runs on heroku, it simply takes a Platform Event and sends publishes it to MQTT, and it listens for MQTT events from the smart power strip and publishes them as a Platform Event. The code for this is available [here](https://github.com/mickwheelz/salesforce-iot-demo/blob/master/mqtt-pe.js).

Now comes the fun part, Salesforce! The first part is of course, platform events. For this to work, we have three platform events defined

1. Smart Meter Reading — This event is published to from the device and contains the current power consumption in watts
2. Smart Meter Event — This event is published to from Salesforce IoT and contains the type of chatter alert to generate
3. Smart Device Event — This event is published from Lightning and subscribed to by the device to turn sockets on and off
   We also have some standard salesforce objects defined these are;
4. Smart Home — A collection of devices under one roof
5. Smart Device — The smart device itself, child of ‘Smart Home’
6. Smart Device Pin — An individual pin (relay in this case) on the smart device, child of ‘Smart Device’
   These objects are used to store basic information such as Device ID, Pin Number and current state. We could also hang reporting from these objects to measure changes in state over time, such as power consumption on a daily/weekly/monthly basis or how often devices are on or off.

When the device is powered on, every 5 seconds, a ‘Smart Meter Reading’ event is generated from the hardware, this is subscribed to in salesforce in two ways; The first being Salesforce IoT, which in itself creates another platform event (‘Smart Meter Event’) if power usage goes over a certain level

![](/img/0*1LhfE36Y0_-_xyvd.png)The IoT orchestration itself fires another platform event, which is subscribed to via an apex trigger. When an event is received the apex trigger creates an appropriate chatter post to warn the user that their usage is high

![](/img/0*Qv_B7Mma4V-4PXMC.png)The second way is directly in a lightning component, this component displays a gauge (built using [gauge.js](http://bernii.github.io/gauge.js/)) of the current power usage to the user. It is only subscribed when the page is open, not all of the time. There is a good trailhead about how to use platform events in lightning [here](https://trailhead.salesforce.com/en/projects/workshop-platform-events) and the code for my lightning components is available [here](https://github.com/mickwheelz/salesforce-iot-demo/tree/master/src/aura)

![](/img/0*ebEoSOc7nXHvvfjA.png)From this user interface, we can generate ‘Smart Device Events’ that are sent off to the device to tell it what to turn on or off, these are generated via an Apex controller that also lists ‘Smart Device Pins’ that are related to a given ‘Smart Home’

And that's it, now of course this is just a basic example, but as you can see, it's not too difficult to control physical devices from salesforce!