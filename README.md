# WheelOfJoy
WheelOfJoy is an Open Hardware 8-player joystick adapter originally designed for the Commodore 16, 116 and Plus/4.

![Board](https://raw.githubusercontent.com/SukkoPera/WheelOfJoy/master/img/render-top.png)

It also works on the VIC20, Commodore 64, Commodore 128 and even on the Commodore PETs and the CBM-II and can easily be used on any platform that provides an 8-bit bidirectional port (or 5 input pins and 3 output pins).

## Summary
The original intent was to figure out how [Solder's 3-joystick Adapter](https://plus4world.powweb.com/hardware/3fach_Joystickadapter_Synergy) worked. It was pretty easy once I realized that the User Port has an output latch, but that also meant that even these 3 extra ports would suffer from the "bouncing ground" effect which prevents using powered controllers.

So I began thinking how to replicate the adapter while avoiding that and suddenly I realized that a very simple solution could be used and that it could easily be extended to support 8 joysticks while retaining compatibility with the original adapter.

One day I found out that WheelOfJoy emulation had been added to [VICE](https://vice-emu.sourceforge.io/) and that it also supported it in the C64 and other emulators, so I used that information to come up with connection diagrams for many more machines.

WheelOfJoy only supports one button per joystick, but it has [a smaller brother](https://github.com/SukkoPera/WheelOfJoyMini) with only four ports that supports 2 buttons per joystick.

The board plugs into the User Port, which means that in order to use it on a C16 or C116, you will need [a User Port card](https://github.com/SukkoPera/16up).

## Assembly
I recommend soldering all the ports first, then the resistor arrays. Speaking of those, note that while RN1-8 are *bussed*, RN9 is *independent*/*isolated*. You can replace the latter with 5 normal resistors soldered on every two adjacent holes.

The adapter can be connected to the computer through a cable with a User Port connector on one side and a male DB-15 on the other. I did this so that the adapter can be placed more freely on the table so that all players can reach it conveniently, but it also turned out to be a good choice in order to be able to use the adapter on different machines, since all you need is a different cable, while the board stays the same. So make sure to label what platform(s) your cable is made for!

The adapter-end of the cable can also be soldered directly to it, if you prefer. Pin names are noted on the board, but here's a handy table:

| DB-15 Pin # | Signal | +4 User Port Pin | PET/VIC20/C64/C128 User Port Pin | CBM-II User Port Pin | Notes                  |
|-------------|--------|------------------|----------------------------------|----------------------|------------------------|
|1	          | +5V    | 2                | 2                                | 24                   |                        |
|2	          | P0     | B                | C                                | 14                   |                        |
|3	          | P1     | K                | D                                | 13                   |                        |
|4	          | P2     | 4                | E                                | 12                   |                        |
|5	          | P3     | 5                | F                                | 11                   |                        |
|6	          | /RESET | 3                | 3                                | N/A                  | Not used by this board |
|7	          | +9V    | 10               | 10                               | N/A                  | Not used by this board |
|8	          | GND    | 1, 12, A or N    | 1, 12, A or N                    | 1 or 3               |                        |
|9	          | +5V    | 2                | 2                                | 24                   |                        |
|10	          | P4     | 6                | H                                | 10                   |                        |
|11	          | P5     | 7                | J                                | 9                    |                        |
|12	          | P6     | J                | K                                | 8                    |                        |
|13	          | P7     | F                | L                                | 7                    |                        |
|14	          | -9V    | 11               | 11                               | N/A                  | Not used by this board |
|15	          | GND    | 1, 12, A or N    | 1, 12, A or N                    | 1 or 3               |                        |

Some pins are not used because I plan to use this same pinout in the future for any other boards I may make requiring one.

**NOTE: I only tested the adapter on the C16/C116/+4. Connection information for all the other machines has been [reverse-engineered from VICE](https://sourceforge.net/p/vice-emu/code/HEAD/tree/trunk/vice/src/userport/userport_woj_joystick.c) and is provided in the hope that it will be useful, without any warranty** (Not that I provide any warranty in any case, LOL!). Please report any issues you might encounter.

[This](https://www.thingiverse.com/thing:3368773) and [these](https://www.thingiverse.com/thing:5213203) will help you make a good-looking job with your cable.

I estimate all the components required to self-build this thing to cost < 10€, probably closer to 5€ if you get them from China.

## Supported Games
Of course there isn't much software support for this at the moment but Luca/FIRE was kind enough to make [Tron 8 WOJ](https://plus4world.powweb.com/software/Tron_8_WOJ), an hack of Solder's [Tron 6](https://plus4world.powweb.com/software/Tron_6) (which can still be used in order to test the "Solder compatible mode") featuring 8 players that can all be controlled through joysticks connected to a WheelOfJoy. This is probably the first 8-player game for the x264 series ever made!

[Haegar](https://plus4world.powweb.com/members/Haegar) was the first developer to make a new game that supports WheelOfJoy natively: it is called [Bombtank](https://plus4world.powweb.com/software/Bombtank) and it supports up to 12 players!

On the Commodore 64 front, [T-Pau](https://github.com/T-Pau) has made [Joyride](https://github.com/T-Pau/Joyride/), a joystick test program that which supports many multi-player adapters, including WheelOfJoy.

Now it's up to the developers to write more party games! I'd happily send you some free PCBs if you are interested in making one, just ask!

## Programming
The board uses 5 8-to-1 multiplexers, one per direction plus one for the fire button. Channel selection happens in parallel on all multiplexers and is done with the 3 high bits of the User Port:

1. Write a value N at the User Port register depending on the port you want to read. General formula for port P ∈ [1,8] is the following:

   ```N = ((P - 1) << 5) | 0x1F```

   Or just pick your value from the following table:

   | # | Binary    | Hex| Dec | Notes                        |
   |---|-----------|----|-----|------------------------------|
   | 1 | 000 11111 | 1F |  31 |                              |
   | 2 | 001 11111 | 3F |  63 |                              |
   | 3 | 010 11111 | 5F |  95 |                              |
   | 4 | 011 11111 | 7F | 127 | Port 6 in Solder's numbering |
   | 5 | 100 11111 | 9F | 159 |                              |
   | 6 | 101 11111 | BF | 191 | Port 5 in Solder's numbering |
   | 7 | 110 11111 | DF | 223 | Port 4 in Solder's numbering |
   | 8 | 111 11111 | FF | 255 |                              |

2. Read the value of the User Port register, the lower 5 bits will report direction/button status:

   | Bit 7 | Bit 6 | Bit 5 | Bit 4 | Bit 3 | Bit 2 | Bit 1 | Bit 0 |
   |-------|-------|-------|-------|-------|-------|-------|-------|
   |   X   |   X   |   X   | Fire  | Left  | Right | Down  | Up    |

   Every bit will be 0 if the corresponding direction/button is pressed, 1 otherwise.

This means that the board works exactly like the one from Solder but the selection value is not restricted to those having exactly one zero. All values will select the corresponding port, software compatible with Solder adapter will select ports 7, 6 and 4 as 4, 5 and 6 respectively (Solder's numbering counts joystick 3 as the one available on [SID cards](https://github.com/SukkoPera/ReSeed)).

The multiplexers used on the board are analog, so the adapter is bidirectional and the ports can also be independently used as 5-bit output ports, which might open interesting possibilities...

## Compatibility
Any Atari-compliant joystick or joypad should work with this board, including Sega MegaDrive/Genesis controllers, as **pin 5 is connected to +5V**.

The latter also means that you MUST NOT use the adapter with anything that can pull pin 5 straight to ground, such as 3-button Amiga mice (what would be the purpose of that anyway?).

Also keep in mind that the User Port is only guaranteed to provide 100 mA on the +5V pin, even though I don't think there's anything actually limiting that.

## Releases
If you want to get this board produced, you are recommended to get [the latest release](https://github.com/SukkoPera/WheelOfJoy/releases) rather than the current git version, as the latter might be under development and is not guaranteed to be working.

Every release is accompanied by its Bill Of Materials (BOM) file and any relevant notes about it, which you are recommended to read carefully.

## Enclosure
A 3D-printable enclosure is currently available for this board, it was kindly contributed by Haegar and it is available on [Thingiverse](https://www.thingiverse.com/thing:6638975).

## License
The WheelOfJoy documentation, including the design itself, is copyright &copy; SukkoPera 2022 and is licensed under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).

This documentation is distributed *as is* and WITHOUT ANY EXPRESS OR IMPLIED WARRANTIES whatsoever with respect to its functionality, operability or use, including, without limitation, any implied warranties OF MERCHANTABILITY, SATISFACTORY QUALITY, FITNESS FOR A PARTICULAR PURPOSE or infringement. We expressly disclaim any liability whatsoever for any direct, indirect, consequential, incidental or special damages, including, without limitation, lost revenues, lost profits, losses resulting from business interruption or loss of data, regardless of the form of action or legal theory under which the liability may be asserted, even if advised of the possibility or likelihood of such damages.

## Support the Project
If you want to get some boards manufactured, you can get them from PCBWay through this link:

[![PCB from PCBWay](https://www.pcbway.com/project/img/images/frompcbway.png)](https://www.pcbway.com/project/shareproject/WheelOfJoy_Commodore_16_116_4_8_Player_Joystick_Adapter_059c7037.html)

You get my gratitude and cheap, professionally-made and good quality PCBs, I get some credit that will help with this and [other projects](https://www.pcbway.com/project/member/shareproject/?bmbid=41100). You won't even have to worry about the various PCB options, it's all pre-configured for you!

Also, if you still have to register, [you can use this link](https://www.pcbway.com/setinvite.aspx?inviteid=41100) to get some bonus initial credit (and yield me some more).

You can also buy me a coffee if you want:

<a href='https://ko-fi.com/L3L0U18L' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://storage.ko-fi.com/cdn/kofi6.png?v=6' border='0' alt='Buy Me a Coffee at ko-fi.com' /></a>

## Credits
* Thanks to Solder for being such a prolific hacker and a great inspiration :).
* 3D model of [DB-9](https://grabcad.com/library/d-sub-9-pin-male-1) and [DB-15 connector](https://grabcad.com/library/d-sub-15-pin-female-2-lines-1) by Alex K.
* Font used for port numbers is [Agreement Signature by wepfont](https://www.fontspace.com/a-agreement-signature-font-f52534) (800 DPI).
* Font used for the logo is [Senja Santuy by Zuzulgo](https://www.fontspace.com/senja-santuy-font-f74971) (450 DPI).
