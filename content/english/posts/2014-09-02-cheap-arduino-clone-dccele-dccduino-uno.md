---
title: Cheap Arduino clone DccEle – DCcduino Uno
author: admin
type: post
date: 2014-09-02T05:55:13+00:00
url: /cheap-arduino-clone-dccele-dccduino-uno/
thumbnail: /images/2014/09/cheap-arduino.jpg
description: Troubleshoot Arduino Uno clone on Mac OS with correct drivers & installation. Learn how to get it working + a simple blink code example.
categories:
  - Arduino

---
Recently I bought very cheaply Arduino UNO Clone called **DccEle &#8211; DCcduino UNO**. Be default It wasn&#8217;t recognized by my Mac OS. After a quick google search, I found this [article](http://forum.arduino.cc/index.php?topic=261375.0) which pointed me to proper drivers.

<!--more-->

![cheap-arduino](/images/2014/09/cheap-arduino.jpg)

If you are not afraid, just pull drivers from this website [www.5v.ru](http://www.5v.ru/ch340g.htm) or this one [www.wch.cn](http://www.wch.cn/downloads.php?name=pro&proid=178). You can also pull copies from here:

  * [ch341ser_mac(v12)][1] 
  * [ch341ser_mac(v13)][2]
  * [CH341SER_MAC][3]

All you need to do is just to install them &#8211; reboot computer &#8211; the new device will appear on your device list:

![arduino-devic](/images/2014/09/arduino-device.png)

And obviously you can upload blink code to your new device:

![blink](/images/2014/09/blink.png)

 [1]: /images/2014/09/ch341ser_macv12.zip
 [2]: /images/2014/09/ch341ser_macv13.zip
 [3]: /images/2014/09/CH341SER_MAC.zip