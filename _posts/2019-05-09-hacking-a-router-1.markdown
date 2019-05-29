---
layout: "post"
title:  "Hacking a router 01: Preparation"
date:   2019-05-09 15:38:24 +0200
categories: hacking embedded
---

## Preparation

I've been hacking for a little while now.
Mostly binary exploitation on x86(_64) or armv7.
But my experiences were limited to software exploitation only,
so I wondered what it would take to get into hardware hacking.

I have to say it takes surprisingly little equipment to get started:

### 1. A cheap device of interest:
Why does it have to be cheap?  
Because you might end up destroying the device. In my opinion something like a cheap chinese router is a good first target. My particular model is the Tenda AC6.  

![The router]({{site.baseurl}}/assets/hackingARouter/01/router.jpg)
    
I initially chose this model after researching a bit 
and reading that it came with linux and an ARM cpu.  
But when I got the device and cracked it open I saw a realtek MIPS cpu.
And further investigation revealed that it doesn't run linux, bummer.
That won't stop me from digging deeper though.

### 2. A USB<->Serial converter:  
Many of those devices have a 2-wire serial interface called UART.
With it you can potentially interface with the device or at least get somedebug output,
which can help you a lot when trying to get in.
    
Pay attention to the voltage it uses, many embedded devices use 3.3V,
connecting a 5V device will cause damage.

![Usb-Serial Converter]({{site.baseurl}}/assets/hackingARouter/01/usbSerial.jpg)

### 3. A multimeter:  
A multimeter is pretty much necessary for any hardware hacking adventure.
It's one of the most important tools to gather knowledge about the circuit.
    
For example the afore mentioned UART port, it often has a 4 pin header.
Those are often GND (Ground), TX (Transmit), RX (Receive) and VCC (SupplyVoltage).
With a multimeter you can figure out which of them GND and VCC are,
leaving you only with 2 connections to guess.
Getting TX and RX mixed up usually won't cause any damage.

![Multimeter]({{site.baseurl}}/assets/hackingARouter/01/multimeter.jpg)

All of this might run you about $50-$60.

That's really most of what you need when you want to start getting your feet wet.
The next post will go into the meat of cracking the device open and getting to know the hardware.