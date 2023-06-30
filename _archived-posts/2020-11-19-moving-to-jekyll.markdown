---
layout: post
title: Moving to Jekyll!
date: 2020-11-19T19:24:21.000+00:00
categories: update
image: "/img/logo-2x.png"

---
![Header Image](/img/logo-2x.png)

It's been quite a while since I've put any effort in to my blog. With the mess than is 2020 it's been pretty hard to find the motivation to do much of anything, let alone write. But sometimes it comes from strange places.

I was running my blog on Wordpress, on a VM in Google Cloud Platform. This was fun for a little while, but I soon realised that I couldn't be bothered to maintain it... and it wasn't exactly cost effective.

I was cross-posting to medium anyway, so I made the decision to shut down the VM hosting my site. I figured i'd save some money and no one was reading it anyway, and I certainly couldn't be bothered to write at the moment. Prior to this I had also moved a bunch of awful code referenced in blog posts in to private repos in GitHub... again, figured no one needed it.

Then a few days ago I had a twitter DM asking about something I'd blogged about and if the code was still available... and that same day a friend let me know my blog was down.

So I tweeted about my intention to just be lazy and blog on medium (if I could ever find the motivation) and I had a few people advise against it... and others tell me they actually did read my blog. There were some great alternative suggestions made to how I could host cheaply/easily.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">I killed off my blog the other day. I didn't want to pay for hosting, & I didn't think anyone was reading it.<br><br>Turns out some people do... Still don't want to pay for hosting so I will be moving everything to <a href="https://twitter.com/Medium?ref_src=twsrc%5Etfw">@Medium</a> . Might even fix the broken links on the posts already there 🤣</p>— Mick Wheeler (@mickwheelz_) <a href="https://twitter.com/mickwheelz_/status/1329430646113099778?ref_src=twsrc%5Etfw">November 19, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

I took a look in to these, and also took a look into jekyll, as another friend of mine was hosting their [blog](https://www.blog.dsb.dev/) on GitHub pages using it. I found that it ticked the boxes... It required no "hosting", was pretty easy to set up, was easy to write in (you write in markdown, and can do it from GitHub's web interface) and wouldn't cost me anything (I was already paying for GitHub Pro and my domain).

In the end, I ended up with [Jekyll](https://jekyllrb.com/docs/github-pages/) and [GitHub Pages](https://pages.github.com/). I am using the [cayman-blog](https://github.com/lorepirri/cayman-blog) theme and my DNS/domain registration is handled by [Cloudflare](https://www.cloudflare.com/products/registrar/).

After getting it setup, I needed to migrate my posts over. I found a [useful little script](https://bthdonohue.com/2018/11/18/medium-to-jekyll-conversion-tool.html) that takes an extract from medium and converts the posts to markdown, including organising and embedding any images.

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Migrating was easier than expected. I was already cross posting to medium and I found a neat little script from <a href="https://twitter.com/bthdonohue?ref_src=twsrc%5Etfw">@bthdonohue</a> that converts your medium posts to markdown for jekyll<br><br>He's written about it here<a href="https://t.co/NhTDVfJHqL">https://t.co/NhTDVfJHqL</a><br><br>Took all of 5mins, excellent.</p>— Mick Wheeler (@mickwheelz_) <a href="https://twitter.com/mickwheelz_/status/1329542713163853830?ref_src=twsrc%5Etfw">November 19, 2020</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

It worked perfectly, and as you can see all of my old posts are now on here. I will need to spin through them to update any broken links, but that won't take long.

This whole move, and the fact people _do_ actually read my blog has made me think about blogging again. The rest of the site is still a work in progress, but should also be done shortly.

I even have some ideas for some new posts about various things I've done recently. Not promising anything, but hopefully in spite of 2020 I can get some motivation to write again!