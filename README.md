# Introduction 
The Blitzen 24 is a 24 port E1.31 pixel controller using the Parallax Propeller 2 (P2)
[![Video Example](http://i3.ytimg.com/vi/Dj3J5ynzC24/hqdefault.jpg)](https://youtu.be/Dj3J5ynzC24)
# Current State
Evaluation boards are available. Please contact me at ttrapp at ieee dot org if you would like to purchase one. 

#To Do 
* Update SD card driver to be cogless. (Free up a cog)
* Complete configuration for quad DMX output.
* Add selectable network speed configuration (10/100/Auto).
* Add "test pixels" option to outputs page.

# Known Bugs
* Webserver files over 64K cause the system to crash. This can be resolved by rewriting how the filesystem is accessed.

# Build 
This software has only been tested using the Propeller Tool.

# Contribute
If you wish to contribute, please email me. ttrapp at ieee dot org