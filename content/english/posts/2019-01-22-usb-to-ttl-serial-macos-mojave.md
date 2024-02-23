---
title: USB to TTL / Serial MacOS Mojave
author: admin
type: post
date: 2019-01-22T14:44:50+00:00
url: /usb-to-ttl-serial-macos-mojave/
thumbnail: /images/2019/01/usb-ttl-serial.jpg
categories:
  - Arduino
  
---
Got a bunch of these little f*ckers and couldn&#8217;t make them to work with my Mojave fresh install. Previously they were working, and now they are gone.

I wasn&#8217;t sure what exactly I&#8217;ve got. I just run [Serial Detect](https://www.mac-usb-serial.com/) to identify what model do I own:<figure class="wp-block-image">

![mac usb serial](/images/2019/01/Screenshot-2019-01-22-15.32.27.png)

Then I googled driver for MacOS Mojave. Because original website: [CP210x USB to UART Bridge VCP Drivers](https://www.silabs.com/products/development-tools/software/usb-to-uart-bridge-vcp-drivers) likes to file, I made a mirror on my server. So you can pull drivers directly [from here][1].

 [1]: /images/2019/01/Mac_OSX_VCP_Driver.zip