---
title: Simplest Arduino Clock With 7-digit segment display
author: admin
type: post
date: 2013-11-30T02:10:58+00:00
url: /simplest-arduino-clock-with-7-digit-segment-display/
thumbnail: /images/2013/11/IMG_0328-310x310.jpg
categories:
  - Arduino
tags:
  - clock

---
From time to time, I&#8217;m also working on less &#8220;web&#8221; related project. This time, a wanted to play a bit with Arduino platform. The task was pretty simple &#8211; to build a clock. Yes &#8211; these days, we don&#8217;t have enough time measuring devices like mobiles, microwaves, computers &#8211; I wanted to build another one.

It was my first Arduino project so the task wasn&#8217;t &nbsp;as easy as I expected. In the theory, we need only pass time & data from RTC to 7-Segment Display. Simple isn&#8217;t it? I found plenty of tutorials, had so many small issues like &#8211; digits wasn&#8217;t displayed correctly (mixed cables). I could only display one digit at the time in given position or &#8211; same digits everywhere (multiplexing). I couldn&#8217;t find shift register in my stock, so wanted to use whatever I already had.

<!--more-->

I already was a proud owner of the whole set, which was:

  1. Arduino UNO R3,
  2. DS1307 Real Time Clock,
  3. 7-Segment Display,
  4. bunch of resistors, cables, and plenty of free time

|7-Segment Display|DS1307 Real Time Clock|Real-Time-Clock-Module|
|---|---|---|
|  ![](/images/2013/11/7-Segment-LED-Display-4-Digits-Common-Anode-High-Light-Red-KHN40561-.jpg) | ![](/images/2013/11/Tiny-RTC-I2C-DS1307-Real-Time-Clock-Module-ARM-PIC-for-Arduino-1_10-more-1.jpg) | ![](/images/2013/11/Tiny-RTC-I2C-DS1307-Real-Time-Clock-Module-ARM-PIC-for-Arduino-1_10-more-3.jpg)

working maginc

![](/images/2013/11/IMG_0326.jpg)


&nbsp;

&nbsp;

Let&#8217;s cook!

  * [SevSeg library](http://playground.arduino.cc/Main/SevenSegmentLibrary) &#8211; even I wasn&#8217;t using SparkFun display,
  * [DS1307RTC Library](http://www.pjrc.com/teensy/td_libs_DS1307RTC.html)&nbsp;&#8211; library to read data from DS1307RTC

&nbsp;

## SevSeg setup

From read.me file:

> The first argument (boolean) tells whether the display is common cathode (0) or common&nbsp;anode (1).  
> The next four arguments (bytes) tell the library which arduino pins are connected to&nbsp;the digit pins of the seven segment display. Put them in order from left to right.  
> The next eight arguments (bytes) tell the library which arduino pins are connected to&nbsp;the segment pins of the seven segment display. Put them in order a to g then the dp.

That was the hardest part of my whole build.&nbsp;Setting up SevSeg to work with my wiring.

`sevseg.Begin(0, 4, 3, 2, 1, 12, 11, 10, 9, 8, 7, 6, 5);`

Mine was pretty simple. I had a version with common cathode &#8211; the first digit is 0, then &#8211; pins connecting t a cathode (digit handling) and then segments displays &#8211; in my case digital outputs in androino 6-12 (A to G) Number 5 points to dot digit.

<!--more-->

```
#include "SevSeg.h"
#include <DS1307RTC.h>
#include <Time.h>
#include <Wire.h>
//Create an instance of the object.
SevSeg sevseg;
void setup() {
    //I am using a common anode display, with the digit pins connected
    sevseg.Begin(0, 4, 3, 2, 1, 12, 11, 10 , 9, 8, 7, 6,5);`

    //Set the desired brightness (0 to 100);  
    sevseg.Brightness(90);  
}  
void loop() {  
    tmElements_t tm;  
    int time;  
    int dot;  
    if (RTC.read(tm))
    {  
        time = tm.Hour * 100;  
        time += tm.Minute;  
    }  
    
    if ((tm.Second % 2) == 0)  
        dot = 4;  
    else  
        dot = 2;  
    
    //Produce an output on the display  
    sevseg.PrintOutput();  
    sevseg.NewNum(time, dot);  
}
```