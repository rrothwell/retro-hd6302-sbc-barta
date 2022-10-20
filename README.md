# retro-hd6302-sbc-barta
Construction notes for a minimalist microcomputer based on the HD6303 IC.

## Introduction

This board was published on [Seed Studio](https://www.seeedstudio.com/Hitachi-HD6303-Single-Board-Computer-(SBC)-g-1017489) in September 2017, by a user called Barta. The actual author is unknown (its not me), but I'll add proper attribution when I discover it.

I have reverse engineered the schematic and assembled the board. 

The integrated circuit runs in multipexed address/data bus mode, as a microcomputer with a full 64k of accessible memory.
The 32K x 8 EPROM contains the Lilbug monitor program and will eventually contain a 6800 FIG forth.
The 32K x 8 RAM chip uses a skinny DIP package and it's hiding under the EPROM, 
soldered directly to the PCB.

The board exposes an 8-bit parallel IO port on an edge connector.
Another 3 bits (these set the mode) and some control signals are exposed on another edge connector.

The 4.9152 MHz crystal is one of those annoying odd frequencies required to obtain a standard baudrate.

A common USB to serial module is used. 
The pinout of these modules is not stardardised, so the module is connected via ribbon cable.

Power to the board is supplied via the edge connection through a 5V regulated wall wart.
This is a temporary measure during construction, as power during normal operation 
is derived from the USB-TTL UART configured to provide 5V.

## Monitor

The monitor program originates with Motorola and its called Lilbug. 
Its quite small and adaptable via additional commands.

Lilbug is loaded into the memory space at $F800 to $FFFF.

The manual is readily findable on the web and it includes the source code.
For example: [Bitsavers](http://www.bitsavers.org/components/motorola/6801/MC6801RM_AD2_MC6801_Reference_Manual_May84.pdf)
However this is a scan in the form of a PDF document and not in a form that can be assembled.

An assembly listing can be found at [Vintage Chips](https://vintagechips.wordpress.com/2018/02/04/sbc6303完成間近/) in Japan, 
as part of a document package for another HD6303-based computer, the sbc6303. 
Seen here in [Japanese](https://vintagechips.wordpress.com/2018/04/26/sbc6303%E3%83%AB%E3%83%BC%E3%82%BA%E3%82%AD%E3%83%83%E3%83%88/) 
and [English](https://vintagechips-wordpress-com.translate.goog/2018/04/26/sbc6303%E3%83%AB%E3%83%BC%E3%82%BA%E3%82%AD%E3%83%83%E3%83%88/?_x_tr_sl=ja&_x_tr_tl=en&_x_tr_hl=en-US&_x_tr_pto=wapp) translation.

![Location of 6303 Docs Japanese](https://user-images.githubusercontent.com/1712402/196881896-4d0dfa65-62da-4f17-b69b-b28a6267cfa2.PNG)
![Location of 6303 docs](https://user-images.githubusercontent.com/1712402/196881951-6e90a15e-3af9-484c-8cab-a6b3b4c16ed6.PNG)

The document package is also linked to directly from [Seeed Studio](https://www.seeedstudio.com/SBC6303-g-1187477).

An alternate assembly listing (not used) is found on [Github](https://github.com/tgtakaoka/LILbug).

The afrementioned document package includes as assembler output the hex and S-record files. 
The hex file was used directly to program an EPROM.

## Programming the EPROM

Programming the EPROM is performed using a universal EPROM programmer, the TL866 II plus.
This is connected to a PC-style disktop computer via USB.

![TL866 II plus](https://user-images.githubusercontent.com/1712402/196881213-45d47329-e129-4361-9dbd-a758afa0381a.JPG)

The associated software is called Xgpro.

![Capture](https://user-images.githubusercontent.com/1712402/196880263-2e86493c-fd71-4a5c-bb9a-af3f78a75ce8.PNG)

The hex file is transferred to a PC-style disktop computer and the contents of the hex file loaded into Xgpro.
The destination of the hex file content in the computer memory map is recorded in the hex file,
so that becomes the file starting address for the load.
The destination address is relative to the address range of the EPROM to be programmed.

![Load Hex](https://user-images.githubusercontent.com/1712402/196880339-18f93887-bd76-4dd6-8275-ce80e24b86d5.PNG)

The successful load can be verified by examining the end of the file buffer 
and identifying the welcome message and the HD6303 vector block.

![Verify load](https://user-images.githubusercontent.com/1712402/196880715-a665a3f3-613d-4231-9de3-dd3ce38bd14f.PNG)

Programming the EPROM is then straight forward, though disabling the ID check helps.

![Program EPROM](https://user-images.githubusercontent.com/1712402/196881535-38d87346-595c-41d4-b1c4-0fae8291bc74.PNG)


### Initial configuration
![IMG_5484 2](https://user-images.githubusercontent.com/1712402/196090757-239bce5f-099a-4dab-af43-f08a50b5a755.jpg)

### Final configuration

![IMG_5498](https://user-images.githubusercontent.com/1712402/196854200-3afc4944-1a5d-4e9e-a85e-cff66e803f4b.JPG)

## Serial Communications

The micromputer to USB dongle pin association is as follows:

| Microcmputer (4 pins) | USB dongle (6 pins) |
| --------------------- | ------------------- |
| 5V                    | 5 V |
| GND                   | GND |
| Rx                    | Tx |
| Tx                    | Rx |

The monitor assembly code has been edited and reassembled (not by me) to provide 9600 baud by default, instead of the default 300 baud.
Note that some USB dongles provide power, but not signals. Use one of the latter.

On a Macintosh or other unix-like desktop computer, the terminal can be used to identify the USB dongle.

`
ls -al /dev/tty*
`

This produces a result that looks like this:

`
/dev/tty.usbserial-AB0LZ57U
`

This only produces a result if the USB dongle is plugged into a USB socket on the desktop computer. 
Then the connection to the "HD6303 Single Board Computer" can be established by issuing a command like:

`
screen /dev/tty.usbserial-AB0LZ57U 9600
`

Pressing the reset button on the SBC should produce a welcome message.
Issuing a Lilbug command such as

`
D F800 F900
`

produces this result:

![Screen Shot 2022-10-20 at 3 36 19 pm](https://user-images.githubusercontent.com/1712402/196857619-6397128e-36a6-4e02-8bd9-75d331dd4b4c.png)

Lilbug on the Macintosh does not understand form-feed or backspace/delete.
