# Introduction 
The Blitzen 24 is an attempt to create a 24 port E1.31 pixel controller using the Parallax Propeller 2 (P2)
[![Video Example](https://img.youtube.com/vi/km8EvoIjuHE/0.jpg)](https://www.youtube.com/watch?v=km8EvoIjuHE)
# Current State
Currently all code is in the development stage. It utilizes several very nice objects from Jon McPhalen, that have been completely hacked up by me :)
This project is dependant on the Wiznet W6100 ethernet controller. You will need the evaluation board until a custom board for the Blitzen is created.

https://eshop.wiznet.io/shop/module/network-module/wiz610mj/

This evaluation board has the parallel interface available. No other eval board does.

As of this writing, multiple universes can be mapped into pixel buffers on the same pixel output.

#To Do 
* ~~Multiple pixel outputs per cog.~~
* Cleanup pixel buffer so that it will accomodate RGBW pixels. (Convert to a single lon on input.)
* Create a facility so that multiple cogs can access the W6100, with priority to the E1.31 cog.
* Create feature so that the configuration can be managed by a web browser and saved to SD card.

# Known Bugs
* ~~Color tearing is occouring on pixels that have very breif display times. I suspect this is related to the input from E1.31 being written to the buffer as bytes instead of longs.~~ 

# Build 
This software has only been tested using the Propeller Tool.

# Contribute
If you wish to contribute, please email me. ttrapp at ieee dot org