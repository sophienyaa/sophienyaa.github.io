---
layout: post
title: Adding EFI to my Monkey Bike, Part One
date: 2021-08-09 18:00:00 +0100
categories:
- monkeybike
image: "/img/img_0941.jpg"

---
If you follow me on social media, you will know I have a bit of an affinity for riding shitty little bikes in interesting places. I've done ['The Monkey Run'](https://www.theadventurists.com/adventures/monkey-run/) in [Morocco](https://www.theadventurists.com/guides/monkey-run-morocco/) and am set to do [Scotland](https://www.theadventurists.com/guides/monkey-run-scotland/) in September 2021 and [Mongolia](https://www.theadventurists.com/guides/monkey-run-mongolia/) in 2022.

I also happen to own a _glorious_ Skyteam ST125, which is a Chinese clone of the classic Honda Z50 Monkey bike but with a 125cc engine, and the bigger brother to the Skytram ST50s used on The Monkey Run.

![Skyteam ST125](/img/img_0941.jpg "Beautiful ain't she?")

Despite causing me issues almost every time I do any sort of distance on it, I like my awful little monkey bike and have done quite a lot of work on it... and ridden it in quite a few places it's not meant to go!

![Monkey Bike off road](/img/fosseway.jpg "Some light off roading")

Here is a quick run down on the changes/work I've done since owning it;

* Swapped in a 140cc Lifan engine, this replaced the original Lifan 125cc after munching the piston and being stranded on the side of the M4... Before you ask, Yes I can ride it on the motorway; it's over 50cc, can do 60mph and I've got a full license.
* Upgraded indicators and lights and switchgear.
* Upgraded gauge cluster, now shows _both_ RPM and speed!
* Camo-green paint, with white star on the tank (was black originally)
* Upgraded tyres; Bridgestone Trailwings. They suck only slightly less than the originals but you don't have a lot of choice with wheels this small.
* Various service items such as battery, oil, air filters, brake pads and fluid, fork rebuild and fork oil.
* Tidy up of the wiring loom (removed electric start wiring, removed unneeded headlight wiring, added wiring for accessories)
* Added a wireless phone charger/holder.

There are probably a bunch of small changes I've missed, but you get the general idea.

So "why EFI?" you ask. Well the main reason, and the reason I do a lot of things that I do is "why not?"

Aside from that, here are a few _real_ reasons;

EFI is interesting to me, and adding it to this bike will be a fun challenge. Most of the bikes and cars I've owned and worked on have been EFI as well, so I am more comfortable with it.

Carbs are a pain in the arse to get right. Yes lots of people love them and swear by them, and yes they are 'simpler', and that is fine, but they annoy me and it's my bike, so there.

Hopefully the bike will run better in more varying conditions. Right now cold starts are a pain.

I've had this idea knocking around for a while, and every so often I will get on eBay/Aliexpress/etc and see what I can find. I have tossed up buying parts individually and making it work with something like a MicroSquirt or SpeedDuino, or even attempting my own ECU build (HA!) and I've looked at kits designed for small engines that come with everything you need (in theory...)

In the end, I found [this](https://www.aliexpress.com/item/32786077851.html) kit on Aliexpress and figured for £190, it was worth a shot. Worst case if the ECU was useless it came with most of the hardware I needed and seems to be aimed at the small horizontal 'honda' type engines, like the one in my bike.

I also found that 'Robot Cantina' on youtube tried this kit on their 420cc stationary engine powered Honda Insight. They start covering it in [Episode 19](https://www.youtube.com/watch?v=RKe1_gTOsms). It's a cool project, so check their channel out [here](https://www.youtube.com/channel/UC_e1W-VZ1ygjbKQGjXB1f4g).

So with all that out of the way, let's get started!

The kit turned up surprisingly quickly, so I unpacked it and took a look at all the components, it all seemed to be there so I thought I'd do a quick bench test first.

I laid it all out, installed the software it came with and powered it on. It was detected by the software and the fuel pump spun up (I then disconnected it, not a great idea to run pumps dry for any length of time). In the 'dashboard' of the software, I could see that the various sensors were working as expected.

![bench testing the kit](/img/bench.jpg "Ignore the mess!")

The kit comes with the following items;

1. ECU itself, marked as a 'motoEFI', it seems to be a clone of the MicroSquirt with some limitations.
2. CDI box, this is a 6 wire GY6 type CDI box, only marked with 'motoEFI'.
3. 3 Bar fuel pump, this has an inbuilt fuel pressure regulator referenced to atmosphere (so no good for boosted applications).
4. Throttle body with combined Map Sensor, Throttle Position Sensor, Intake Air Temperature Sensor and Idle Speed Control solenoid.
5. Injector, has a right angle fuel feed and a Bosch EV1 style plug.
6. 4-wire heated narrowband O2 sensor.
7. The wiring loom itself including a Check Engine Light and a Engine Temp Sensor
8. Fuel line, clips and various other little fixings.
9. A download link for the software and a tuning manual.

All in all, basically everything you need (in theory...) to add EFI to a small engine.

Now, the kit as it is, requires only three extra wires to work, +12V, GND (as you'd expect) and an ignition pulse.

On the engine in the monkey bike, this signal comes from a small magnet on the flywheel and a pickup in the engine side case. It's actually the same signal that controls the original CDI box on the bike.

I figured the next thing to do then, was to try it out on the bike itself. It's obviously not tuned or set up for the bike (yet!), but I wanted to see if it would at least try to run it, and to make sure all the parts fit as expected.

So I went about temporarily installing it, the main thing I had to do was remove the carburettor to install the throttle body assembly. I then connected the fuel line from the tank to the inlet of the pump, ran a hose from the pump to the filler cap as a return and a hose to the injector.  
  
I connected the ECU loom's power and ground wires directly to the battery and jammed the ignition wire in to the appropriate place on the engine loom. On doing that I heard the fuel pump whirr and saw the lines fill up with fuel. No magic smoke was coming out of anything so it was time to give it a kick it and see what happens.

I gave it a kick.... nothing.

I gave it another kick... it half fired.

I gave it a third kick... it fired and began idling perfectly!  
  
I filmed a quick video of it running, click the image below to see it.

[![Monkey Bike running using EFI](https://img.youtube.com/vi/KER29pNNHlY/0.jpg "It's Alive!")](https://www.youtube.com/watch?v=KER29pNNHlY)

I was absolutely staggered. I figured it would cough and splutter and that would be enough to prove it worked.

But it was just sitting there idling perfectly. The bike was cold at this stage, and with the carburettor it never idled well cold.

I gave the throttle a couple of blips and it happily revved up.

Well, that is that sorted then, it seems pretty likely this will actually _work_.

As I am intending to use the bike for the upcoming [Mongol Rally X](https://www.theadventurists.com/adventures/mongolrally-x/), and don't have time to finish this project before then, I put the carburettor back on and called it a day.

I think I'd call that a success.

That will do for part one, in part two we will dig deeper how I plan to make the wiring work!
