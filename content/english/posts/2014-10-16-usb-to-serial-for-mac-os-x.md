---
title: USB to Serial for Mac OS X
author: admin
type: post
date: 2014-10-16T03:30:14+00:00
url: /usb-to-serial-for-mac-os-x/
categories:
  - Arduino

---
In this quick tutorial will show how to &#8220;talk&#8221; quickly to Serial port on Mac OS X.

Mac OS X will automatically create a serial device, which can be found in /dev.

Start a terminal session and list all available serial (or tty) devices available.

<!--more-->

`ls -l /dev/tty*`

Find the entry that is referring to your device. It could looks like that:  
```
crw-rw-rw- 1 root wheel 2, 0 16 Oct 14:23 /dev/tty
crw-rw-rw- 1 root wheel 19, 0 16 Oct 08:09 /dev/tty.Bluetooth-Incoming-Port
crw-rw-rw- 1 root wheel 19, 2 16 Oct 08:09 /dev/tty.Bluetooth-Modem
crw-rw-rw- 1 root wheel 19, 10 16 Oct 14:25 /dev/tty.HC-05-DevB
crw-rw-rw- 1 root wheel 19, 4 16 Oct 08:09 /dev/tty.iPhoneslav-WirelessiAP
crw-rw-rw- 1 root wheel 19, 6 16 Oct 08:54 /dev/tty.wch ch341 USB=>RS232 1420
```
Now that we have identified our new serial device, we can start connecting into it. Most people will use a Terminal Emulator, but as most of these programs were built. We simply use the built-in screen program on Mac OS. So in the same terminal window, simply type:

`screen /dev/tty.HC-05-DevB 9600`

You can obviously change the baud rate or any other options to what you need to connect to your specific device.