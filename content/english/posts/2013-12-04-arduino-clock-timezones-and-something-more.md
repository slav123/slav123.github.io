---
title: Arduino Clock TimeZones and something more
author: admin
type: post
date: 2013-12-04T00:15:42+00:00
url: /arduino-clock-timezones-and-something-more/
categories:
  - Arduino
tags:
  - arduino
  - clock
  - rtc
  - timezones

---
Following with my last project &#8211; [Arduino clock](/simplest-arduino-clock-with-7-digit-segment-display/) &#8211; i wanted to extend it with 2 features:

  1. Handling daylight saving time,
  2. Showing up time in different time zone.

<!--more-->

  
Additional requirement is of course to make it smaller and&#8230; nicer. So step first: replace Arduino UNO with something smaller:

![arduino-mini](/images/2013/12/arduino-mini.jpg)

&nbsp;

Next step: soldiering LCD Display.

I needed additional library [ Arduino Timezone Library](https://github.com/JChristensen/Timezone) & complete [timezone information](http://home.tiscali.nl/~t876506/TZworld.html#aus) for all countries.
