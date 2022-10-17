# retro-hd6302-sbc-barta
Construction notes for a minimalist microcomputer based on the HD6303 IC.

## Introduction

This board was published on [Seed Studio]((https://www.seeedstudio.com/Hitachi-HD6303-Single-Board-Computer-(SBC)-g-1017489) in September 2017 by a user called Barta. The actual author is unknown (its not me) , but I'll add proper attribution when I discover it.

I have reverse engineered the schematic and assembled the board. 

The micro runs in multipexed address/data bus mode as a microcomputer.
The 32K x 8 EPROM will enventually contain the Lilbug monitor program and a 6800 Figforth.
The 32K x 8 RAM chip uses a skinny DIP package and its hiding under the EPROM, 
soldered directly to the PCB.

The board exposes an 8-bit parallel IO port on an edge connector.
Another 3 bits (these set the mode) and some control signals are exposed on another edge connector.

The 4.9152 MHz crystal is one of those annoying odd frequencies to obtain a standard baudrate.

A common USB to serial module is used. 
The pinout of these modules is not stardardised so the module is connected via ribbon cable.

Power to the board is supplied via the edge connection through a 5V regulated wall wart.

![IMG_5484 2](https://user-images.githubusercontent.com/1712402/196090757-239bce5f-099a-4dab-af43-f08a50b5a755.jpg)

