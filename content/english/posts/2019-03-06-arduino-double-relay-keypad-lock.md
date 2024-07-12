---
title: Arduino double relay keypad lock
author: admin
type: post
date: 2019-03-06T09:00:17+00:00
url: /arduino-double-relay-keypad-lock/
description: Learn how to build a DIY Arduino-powered keyless entry system for your garage door using a keypad and double relay. Perfect for those who often forget their remotes
categories:
  - Arduino

---
I needed key-less way to open my garage door. Quite often I forgot my remote, so just need something easy to open them without remote. Because I have regular &#8220;ring type&#8221; opener (click and release to open door) needed something to &#8220;click&#8221; door for me based on something which I remember (not own &#8211; remote / key / etc).

Basic idea &#8211; keypad + arduino + relay to click the button. 

![Keypad](/images/2019/03/arduino-keypad-relay.jpg "Keypad") 


![Prototype board](/images/2019/03/arduino-lock-1.jpg "Prototype board")

Source code is pretty simple. Read input from the keypad, check if it&#8217;s matched against 1, or second 2. If so &#8211; just fire signal to open relay for 3 or 30 seconds (second one is for other door). 

{{< gist slav123 f5062a588a167eabf3806f49081f6d94 >}}
