---
layout:	post
title:	"Supercharging a cheap GPS Tracker — Part 1: Hardware"
date:	2017-08-03
---

  Recently my motorcycle was stolen, which is not an experience I would like to repeat. Motorcycle crime in London is quite prevalent, and unfortunately, I didn’t secure the bike as well as I should have.

Luckily I was able to find and recover it with minimal damage, as it was dumped quite close to my home.

I decided I needed to beef up security, so aside from a chain and disk lock, I also wanted an alarm and a GPS tracker.

Having a look on amazon, I came across the ‘[XCSOURCE Vehicle Tracker](https://www.amazon.co.uk/d/GPS-Navigation-Accessories/XCSOURCE-Vehicle-Real-time-Tracking-Motorcycle-Antitheft-AH207/B01KJS55JS/ref=sr_1_1?ie=UTF8&qid=1501760872&sr=8-1&keywords=xcsource+vehicle+tracker)’ for £14.99. This seemed *very *cheap for a GPS tracker, as I’d seen them advertised for hundreds of pounds in other places, but for £14.99, what do I really have to lose?

It seemed to have the basic features I wanted, with real time tracking, SMS capability and TCP/IP reporting (e.g sending data to a tracking service). I did notice that in the pictures there were many unpopulated pads for other functions, so I thought maybe I could do more with it than advertised.

Turns out my hunch was correct, it was very easy to expand the capabilities of the device, as well as get the data in to a tracking service!

#### **Initial Setup**

You will need a SIM card in order for this device to work, the device uses the 2G (GPRS) network, so make sure the provider you choose still has a 2G network.

In the UK, ee, O2 and Vodafone all provide 2G networks. Three does **NOT.**

I purchased a sim from giffgaff as they use O2’s network and are very cheap. I pay £5/month and get 100mb of data and 500 texts. The device tends to use ~50mb of data per month and ~100 texts, but of course YMMV.

If you just want to use the SMS functionality, all you need to do is pop the sim card in and connect up the device, however there is some configuration required if you wish to use it with a tracking service (which I will cover in my next post)

It is a good idea to change the password for the device, the default is 123456. You can do this by sending password<oldpassword> <newpassword> (e.g “password123456 111111”) to the device vis SMS.

#### **Hardware Modifications**

As shown in the pictures on amazon, you need to open it up to put in the SIM card. You can also see that there are several pads on the board that are unpopulated. This particular GPS tracker (often known as a GT06/GT02) had quite a few more features than listed, and these can be accessed by simply soldering wires to the pads.

![](/img/0*uI2cK6wpc5rdj9Bc.jpg)![](/img/0*FDdS3dEoKB1WwV8B.jpg)I removed the standard wiring, and soldered in new wiring to the following pads

1. ACC — This is for sensing if the vehicle is on, connect it to power that is switched by the ignition
2. OIL — This is used for disabling the vehicle remotely (which i’m not currently using)
3. MIC+ and MIC- These are used for an optional microphone (you can call the device and hear what is going on in the vehicle, no use on a bike but probably handy on a car)
4. + and — on the back of the device — This is for a speaker (you can yell at the person who is stealing your vehicle!)
5. VBATT and GND — This is for your backup battery (more details on that below)
6. GPSTX and GPSRX — This is for the data stream coming from the onboard GPS (more details on that below)
7. TX and RX — This is for the data coming from the device itself (more details on that below)
With that done, you should have something like this.

![](/img/0*J4vkSkA3MV0gWN96.jpg)Once you’ve done that, you can re-assemble the device. With the original wiring removed, the new wires have no issues fitting through the opening for them at the end.

![](/img/0*TN430R0rUOCyJsfu.jpg)#### **Battery Backup**

Probably one of the most useful things you can enable the battery backup. Not only will this help to prevent the GPS tracker from draining your vehicles main battery, it also has a feature that it sends an SMS to you with the devices location as soon as it detects a power cut. So even if thieves find the main wiring and cut it, it will keep transmitting location, as long as the backup battery remains connected/charged.

![](/img/0*GFa_Lb6WJOsOEzAQ.jpg)If you get ahold of a battery mobile phone charger, they normally contain a 18650 3.7v cell, you can use one of these (or any other 3.7v battery really) as backup for the GPS.

Alternatively, you can buy a pair of [18650s](https://www.amazon.co.uk/Samsung-INR18650-25R-UNPROTECTED-Rechargeable-Battery/dp/B00KYRH1JM/ref=sr_1_4?s=electronics&rps=1&ie=UTF8&qid=1495704826&sr=1-4&keywords=18650+battery) here for £10.49 if you don’t have some laying around.

A word of caution though, soldering to batteries is not ideal, you may be better suited either getting a hold of batteries with tabs/wires, or using a [holder](https://www.amazon.co.uk/5Pcs-Battery-Holder-18650-Rechargeable/dp/B010B1QUYM/ref=sr_1_6?ie=UTF8&qid=1495705096&sr=8-6&keywords=18650+holder). If you must solder, be very quick about it as heat is bad for these batteries.

Once you’ve got your battery, you simply connect it to the wires you soldered to the VBATT and GND pads earlier, I have a plug on either end of mine so I can remove it easily if required

#### **Serial Output**

You can gain access to both the device’s serial output, as well as the GPS module’s serial output from the pads on the board, the serial output is useful for debugging, as it reports overall status (e.g battery, GPS, GPRS, etc) every few seconds.

The GPS output is useful if you need data from the GPS for another purpose e.g a custom navigation, digital dashboard, etc.

![](/img/0*SYGnr8N2EXwmAyAk.jpg)The pins ‘GPSRX and GPSTX’ on the back side of the board are for the GPS signal and the ‘TX’ and ‘RX’ near the USB port are for the console. Remember for serial to work you also need a ground, the power ground (‘GND’ at the bottom of the board with the other pads) works fine here. The serial voltage is +5v (so be careful when using it with 3.3v devices like raspberry pi’s). The GPS operates at 9600bps, 8-n-1 and outputs standard [NMEA](https://en.wikipedia.org/wiki/NMEA_0183) sentences. The console operates at 9600bps, 8-n-1 and outputs text.

![](/img/0*WGfeZJIqaxpcomv9.jpg)#### **Next Steps**

Once all this is done, find somewhere inconspicuous to mount the device on your bike or car, and connect all of the lines you wish to use (+12v, ACC, backup battery, etc)

In part two, I will detailing how you can use this in conjunction with software called [traccar](https://www.traccar.org/) for logging and tracking of your vehicle, which really unleashes the potential of this device!

*Originally published at *[*mickwheelz musings*](http://www.mickwheelz.net/2017/08/03/supercharging-a-cheap-gps-tracker-part-1/)*.*

  