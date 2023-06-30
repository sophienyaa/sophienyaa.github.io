---
categories: ["note"] 
title:	"…and now for no reason: Emoji’s in your Wi-Fi name!"
date:	2017-07-26
---

A while ago, I came across someone using emoji as a Wi-Fi network name (SSID), I tried to do the same on my wifi router (I wanted the delightful smiling poo emoji 💩) but my router, sadly wouldn’t let me.

I saw it again the other day, and I thought I’d have another try, after all this was years ago and I have a much newer router, and newer version of DD-WRT running on it.

But, I was rudely told that what I was trying to do was illegal.

![](/img/0*7O9VqnfAJytS4D_Z.png)Not to be deterred, I thought I’d try changing it via SSH… but that was not to be either.

![](/img/0*36s-ge9mATw_oXVq.png)Inserting the emoji returned “p)” which was not accepted, I did also try the unicode char for it “[U+1F4A9](https://apps.timwhitlock.info/unicode/inspect/hex/1F4A9)” but that didn’t work either.

Turning to google, I wondered if anyone had done this successfully before, but all I could find was [this](https://medium.com/@bcjordan/emojify-your-wi-fi-c01f4ac0b0ab) article, This was done on the same model of wireless router as I own, but using the stock firmware.

But it did give me a good idea… So taking the same approach as in the article, but skipping straight to the server-side method, I used chrome dev tools to inspect the request;

![](/img/0*lGdx0AmXnYqGfhyj.png)So that is all well and good, but I need to replicate the request with new parameters, so turning to postman

![](/img/0*6P2BZxQ0gKk51kju.png)Fingers crossed, I hit send on the request… and low and behold!

![](/img/0*NHLy4UGtaN3i7JQq.png)Success!

So far I’ve not had any issues with modern-ish devices finding/connecting to it, however I did leave the 2.4ghz radio of my wireless router alone, so that older devices can use it if need be.


  