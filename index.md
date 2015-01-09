---
layout: default
title: Main Page
---
{% include JB/setup %}

##About

Andrew D'Angelo: I'm a freshman at the University of Washington. When not programming or online I'm usually hanging out at Thorntons or playing Smash Bros.

Programmer in C (GBA & PC), C# (WP8 & Metro), Python, and Scheme. I can be found on [irc.rizon.net](http://rizon.net) under the nick `excel`.

My resumé can be found [here]({{ site.url }}/resume.html)

#### PGP Data

`71D1 5FED` revoked on 7-11-2013

`D0F7 24E7` revoked on 3-6-2014

Fingerprint: `9763 0523 B54B 17E4 0ECE  42C6 F2D9 8C89 394E 5B3A`

Public key: [pubkey.txt]({{ site.url }}/assets/text/pubkey.txt)

#### 3DS Friend Code

`5086 2544 8580`

## Posts

{% for post in site.posts %}
  * {{ post.date | date: "%B %e, %Y" }} &raquo; [{{ post.title }}]({{ post.url }})
{% endfor %}

---

## Apps

### Omeddle

![Omeddle Icon]({{ site.url }}/assets/images/om-icon.png) 

I finished and published this app during Microsoft's //publish/ 2014 event in Chicago, where I won 3rd place in the overall local app competition.

Don't just chat with Omegle users, meddle with them! Omeddle is not only a fully-featured Omegle client (Classic chat, ask and answer questions), but also puts a fun twist on the classic Omegle formula: not only can you chat, you can also control! Sit between two unsuspecting Omegle users and control the conversation by masquerading as the Stranger and modify messages with simple find and replace or regular expressions. 

Also features image sharing and management (upload pictures to share during a chat, as well as view and delete any image from Imgur that was uploaded during previous chats) and easy chatlog sharing with PasteBin!

[![Windows Phone Store 8 Banner]({{ site.url}}/assets/images/154x40_WP_Store_gry.png)](http://www.windowsphone.com/en-us/store/app/omeddle/e99fbcac-c908-43e0-87c0-2c69e394a466)

### Thorntons++

![Thorntons++ Icon]({{ site.url }}/assets/images/tpp-icon.png) 

**Update Sep. 2 2014**: Thorntons++ has been pulled from the Store due to Thorntons changing loyalty and coupon system. This will require a total rewrite of Thorntons++.

Thorntons++ is my first Windows Phone app, an unofficial OpenStore client that allows you to get the latest Thorntons coupons and deals on Windows Phone 8 and to save your FREEquency Rewards card. While the official Android and iOS apps collect personal data such as location, phone number, and phone model, Thorntons++ generates this information randomly. Coupons are not erased after use and can be used offline.

[![Windows Phone Store 8 Banner]({{ site.url}}/assets/images/154x40_WP_Store_gry.png)](http://www.windowsphone.com/en-us/store/app/thorntons/3414a6e0-2f63-4697-88fe-ddd266ccc971)

---

## Projects

### OmeglePCL

![OmeglePCL Icon]({{ site.url }}/assets/images/opcl-icon.png) 

Omegle client Portable Class Library in C#. Current version used in [Omeddle](http://www.windowsphone.com/en-us/store/app/omeddle/e99fbcac-c908-43e0-87c0-2c69e394a466). Due to time constraints, Omeddle is shelved. 

[github repository](https://github.com/excelangue/OmeglePCL)

### Canabalt Advance

![Canabalt ADV Icon]({{ site.url }}/assets/images/cadv-icon.png) 

A recreation of Adam Atomic's hit flash game for the Game Boy Advance. Graphics resources are taken from the open-source iOS port of the game. Uses [Krawall Sound System](https://github.com/sebknzl/krawall). KSS went open-source in 2013, many thanks to Seb! Music to be composed by [Emry Day](http://gplus.to/emry).

[github repository](https://github.com/excelangue/canabaltadv)

### gbcdump

![gbcdump Icon]({{ site.url}}/assets/images/gbcd-icon.png) 

Input Game Boy camera battery saves and output bitmaps. Requires `libbmp`

[github repository](https://github.com/excelangue/gbcdump)
