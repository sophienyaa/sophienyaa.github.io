---
layout: post
title: Building a information display for my CRF250L
date: 2020-11-21T15:00:00.000+00:00
categories: electronics motorcycle
image: "/img/assembled-version.jpg"

---
![Header Image](/img/assembled-version.jpg)

One of the small handful of motorcycles I own is a 2015 Honda CRF250L. I bought it after doing the Monkey Run Morocco in 2019 (maybe I should write about that one day...) and realising this whole off road / adventure motorcycling thing is a lot of fun.

The CRF250L is an excellent beginners adventure bike. It's lightweight, easy to ride and decent off road. It's not an Africa Twin by any stretch, but for someone like me who is starting out, it's great.

That said, there is room for improvement. The bike had a few choice mods when I bought it. Things like a smaller front sprocket (for a bit more low down torque), aftermarket air filter, bar risers, heated grips, hand guards, foldable mirrors and a rear rack.

One of the things I disliked about the bike was how basic its dash was. It has no tach and no temp gauge. You get speed, fuel, time and some lights... that's it.

![stock-dash](/img/dash-stock.jpg)

It has an over-temp light, and obviously you don't _need_ a tachometer, but it is nice to have a better idea of what the bike is doing.

I wondered if, being a somewhat modern bike, there would be some sort of diagnostic port that I could connect to to get this data. As it turns out, there is!

"Modern" Honda motorcycles have a 4 pin connector called a HDS port. This is _similar_ in function to the OBD port in a car. In fact that was one of the first approaches I looked in to, after finding [HDS to OBD adapter](https://www.lonelec.co.uk/Honda-4pin-OBD2-Adaptor) cables online.

After further research (huge shout-out to the [PGM-FI forums](http://forum.pgmfi.org/)) found that Honda does use a standard protocol (KWP2000), but most cheap ELM327 OBD adaptors (including the one I had) are incompatible with it, as it has a non-standard initialisation sequence. I did try it anyway, after making a cable (aka jamming some wires into the HDS port and clipping them to the ELM327 device) I was able to confirm it didn't work.

So I kept looking and stumbled across two things that made me realise this was possible. The first was the [CTX-OBD](https://gonzos.net/projects/ctx-obd/) project that had a similar goal/idea to what I was hoping to do. The second was the KWP2000 library for arduino that Aster94 had on [GitHub](https://github.com/aster94/Keyword-Protocol-2000).

I didn't end up using the KWP2000 library (as it's only been implemented for Suzuki) but I did use the hardware (namely a [L9637D](https://www.st.com/resource/en/datasheet/l9637.pdf) bus driver)

I was able to finally prove the theory with some very ropey code, an Arduino Nano and the L9637D.

![ardu nano](/img/ardu-nano.jpg)

Once the excitement of seeing data from my bike's ECU being displayed on my laptop, I  had to think about how I would build the actual device.

For the display, I decided to use an cheap ILI9341 SPI display, as they are easy to work with and I had a few laying about. For the microcontroller I went with an ESP32. I could've used the Arduino Nano, but the ESP gives me a _much_ faster CPU, more memory, more peripherals and the option to use things like WiFi and Bluetooth in the future. They are also incredibly cheap and I had a few of them kicking around.

I put together a prototype using the ESP32, a display and the L9637D chip.

After much tweaking it all worked and I knew the design was viable, so I designed a PCB in Eagle CAD. I sent it over to PCBWay for fab (my first time using them) and a week or so later these turned up.

![pcbs](/img/pcbs.jpg)

I was very happy with the PCBs. They did have a small mistake, which was entirely my fault and only required cutting a trace. After fixing that and soldering everything on, I was able to flash the firmware I'd written and it was ready to go. I added a few more features like an external temperature sensor (using a DHT22) and a GPS module for speed/heading/etc.

I needed a case, so designed something basic in Fusion360 and 3D printed it. I decided to go with a generic GoPro style mount on the bottom, which I paired with a handlebar mount I had to mount it to the bike.

![assembled-version](/img/assembled-version.jpg)

I mounted it to the bike, and lo and behold, it worked! I took the bike for a ride and discovered a few bugs in the firmware (like the speed being reported in KPH not MPH) but it actually worked!

<div>
<iframe width="560" height="315" src="https://www.youtube.com/embed/F6zy1viY6iY" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe>
</div>

I have done a much more technical write-up on the project's [GitHub repo](https://github.com/mickwheelz/Honda-Motorcycle-Dash), which includes a wiki.

So whats next? Well I've designed a version 2 of the PCB, which has simpler power delivery, and uses a display directly mounted to it. I will cover this new version in a future blog! I also need to do some more work on the firmware, and the documentation, which will come in time.

This should work on other Honda bikes, so if you've got a Honda made in the last 10 years or so, and give it a try please do let me know how you go!