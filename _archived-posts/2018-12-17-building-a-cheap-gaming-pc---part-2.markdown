---
layout: post
title: Building a cheap gaming PC — Part 2
date: 2018-12-17

---
![](/img/0*4uRhWQHPGPSQviq-.jpg)In [part one](https://mickwheelz.net/index.php/2018/12/13/building-a-cheap-gaming-pc-part-1/) of this series, I ran through the build of the first machine, the Optiplex 9010. This was the more ‘high end’ of the builds, and the machine I set out to build in the first place. So what of the 7010 I never intended to order? Well I set myself a challenge to see what I could throw together for \~£100.

With the £36 Optiplex 7010 as a base I decided that 8GB of memory would be enough for it, given that I already had 2x2GB sticks (One from the 9010 and one that the 7010 came with) I went and purchased another 2 of them from CEX for £3.50 each to bring me up to 8GB, which should be just about enough for a gaming system.

Next was the CPU, the Pentium was agonisingly slow and would bottleneck any half decent graphics card, so it had to go! I sprung for a modest, but much better Core i3–3220. It cost me a grand total of £12 from CEX, so the price was right. Next up I needed storage, so at £8 a pop from CEX I grabbed another 500GB mechanical drive. It’s no SSD but it will do for what I am trying to achieve with this build.

Keeping in mind the £100 budget, but still wanting decent performance didn’t leave me with many options. I was already at £63 deep at this point and I still needed a power supply and a graphics card! Again, I wanted to go new for the PSU as it’s something that can bring down the entire system if it goes wrong. Luckily I found a factory refurbished Corsair VS450 on [SCAN PC](https://www.scan.co.uk/products/450w-corsair-vs-series-vs450-fully-wired-80-plus-white-sli-crossfire-single-rail-36a-120mm-fan-atx-p) for £27, so I grabbed it, bringing us up to £90… dangerously close to the £100 budget. I was wondering how I was ever going to get a decent GPU for anywhere even close to £10…

But then I stumbled upon a GTX580 on eBay, it was listed as ‘make and offer’ and marked as ‘not working’ with the description citing a driver issue. I figured it was worth the risk and made an offer of £20, £10 over my budget, but I knew the seller wouldn’t have taken £10! I waited and a few hours later my offer was accepted. The card was a full size one so I knew some modifications would need to be made to the case to make it fit, if I could even get it working properly.

So I got all the parts together and installed Windows 10, and all seemed well except there were no video drivers. On first boot, Windows downloaded the Geforce drivers and installed then, and then I got the same driver error that the seller had said in the listing. I thought this was very strange, given this was a completely fresh install. I rebooted the machine, but this time noticing that there were two ‘out of place’ pipe symbols ( | ) on the VGA bios screen, which concerned me. After googling around and trying a few re-installs I figured it was most likely a hardware issue and I’d wasted £20.

Before I resigned myself to binning the card and jumping back on eBay, I thought I’d try one last thing… The oven! This is a pretty well [documented](https://lifehacker.com/5823227/save-dying-video-cards-with-a-quick-bake-in-the-oven) tactic for fixing broken electronics. The basic idea is that you heat the board up just enough so that it melts and re-flows the solder, re-forming any bad joints and (hopefully) fixing the card.

I stripped the card down to a bare PCB, put it on a baking tray raised up by little foil balls and pre-heated the oven to 200c (\~390f) and baked until golden brown for 8 minutes. I shut off the oven and opened the door, waiting a couple of minutes before carefully removing the card. I applied new thermal grease to the chips and re-installed the heat sink, fans, shrouding, etc that I had cleaned thoroughly while the card was baking.

In order to fit the large card into the 7010’s small chassis I had to make some modifications. I needed to drill out the rivets holding in the lower drive bay (see the picture of the 9010 in my earlier [post](https://mickwheelz.net/index.php/2018/12/13/building-a-cheap-gaming-pc-part-1/), bottom right) and given the heft of the card, I used one of the old PCI slot covers to make a card support bracket. Given that I now didn’t have anywhere to mount the hard disk, I made some adapter brackets out of a couple more of the old PCI slot covers so that I could mount the 3.5in drive in the un-used 5.25in bay. Given the ‘tool less’ design of the case, this was a bit more painful than it could have been, and I may yet order an adaptor online to use, but for now it works fine.

I installed the card in the Optiplex and crossed my fingers… powering the machine on, I saw the VGA bios screen (sans |’s) and the machine booted into Windows. I was pretty excited and then all of a sudden the screen went blank. Crap, my little cooking adventure had been for nothing! But then it came back on, at the right resolution, it was just windows installing the drivers! Cautiously optimistic, I began installing some benchmark software to see if my freshly baked GPU was a) stable, and b) any good.

So here is the final spec of the machine, and the cost of each item.

In part three, we will benchmark both systems and see how they stack up performance/cost wise to some pre-built machines, and see what it would have cost to build similar spec machines with new parts!