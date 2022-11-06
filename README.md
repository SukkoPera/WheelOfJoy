# WheelOfJoy
WheelOfJoy is an Open Hardware 8-player joystick adapter for the Commodore 16 and Plus/4.

![Board](https://raw.githubusercontent.com/SukkoPera/WheelOfJoy/master/img/render-top.png)

## Summary
The original intent was to figure out how [Solder's 3-joystick Adapter](https://plus4world.powweb.com/hardware/3fach_Joystickadapter_Synergy) worked. It was pretty easy once I realized that the User Port has an output latch, but that also meant that even these 3 extra ports would suffer from the "bouncing ground" effect which prevents using powered controllers.

So I began thinking how to replicate the adapter while avoiding that and suddenly I realized that a very simple solution could be used and that it could easily be extended to support 8 joystick while retaining compatibility with the original adapter.

The board plugs in the User Port, which means that in order to use it on a C16 or C116, you will need [a User Port card](https://github.com/SukkoPera/16up).

## Assembly
I recommend soldering all the ports first, then the resistor arrays. Speaking of those, note that while RN1-8 are *bussed*, RN9 is *independent*. You can replace the latter with 5 normal resistors soldered on every two adjacent holes.

The adapter can be connected to the computer through a cable with a User Port connector on one side and a male DB-15 on the other. I did this so that the adapter can be placed more freely on the table so that all players can reach it comfortably. The adapter-end of the cable can also be soldered directly to it, if you prefer. Pin names are noted on the board, but here's a handy table:

| DB-15 Pin # | Signal | User Port Pin # | Notes                  |
|-------------|--------|-----------------|------------------------|
|1	          | +5V    | 2               |                        |
|2	          | P0     | B               |                        |
|3	          | P1     | K               |                        |
|4	          | P2     | 4               |                        |
|5	          | P3     | 5               |                        |
|6	          | /RESET | 3               | Not used by this board |
|7	          | +9V    | 10              | Not used by this board |
|8	          | GND    | 1, 12, A or N   |                        |
|9	          | +5V    | 2               |                        |
|10	          | P4     | 6               |                        |
|11	          | P5     | 7               |                        |
|12	          | P6     | J               |                        |
|13	          | P7     | F               |                        |
|14	          | -9V    | 11              | Not used by this board |
|15	          | GND    | 1, 12, A or N   |                        |

Some pins are not used because I plan to use this same pinout in the future for any other boards I may make requiring one.

[This](https://www.thingiverse.com/thing:3368773) and [these](https://www.thingiverse.com/thing:5213203) will help you make a good-looking job with your cable.

I estimate all the components required to self-build this thing to cost < 10€, probably closer to 5€ if you get them from China.

## Supported Games
Of course there isn't much software support for this at the moment but I tested it with [Tron 6](https://plus4world.powweb.com/software/Tron_6) and it works fine in "Solder compatible mode". To test the whole 8 ports I wrote a simple BASIC program and everything seems to work as it should. Now it's up to the developers to write party games for the C16/+4 for up to 10 players!

## Programming
The board uses 5 8-to-1 multiplexers, one per direction plus one for the fire button. Channel selection happens in parallel on all multiplexers and is done with the 3 high bits of the User Port.

This means that the board works exactly like the one from Solder but the selection value is not restricted to those having exactly one zero. All values will select the corresponding port, software compatible with Solder adapter will select ports 7, 6 and 4 as 4, 5 and 6 respectively (Solder's numbering counts joystick 3 as the one available on [SID cards](https://github.com/SukkoPera/ReSeed).

The multiplexers used on the board are analog, so the adapter is bidirectional and the ports can also be independently used as 5-bit output ports.

## Compatibility
Any Atari-compliant joystick or joypad should work with this board, including Sega MegaDrive/Genesis controller, as pin 5 is connected to +5V.

The latter also means that you MUST NOT use the adapter with anything that can pull pin 5 straight to ground, such as 3-button Amiga mice (what would be the purpose of that anyway?).

Also keep in mind that the USer Port is only guaranteed to provide 100 mA on the +5V pin, even though I don't think there's anything actually limiting that.

## Releases
If you want to get this board produced, you are recommended to get [the latest release](https://github.com/SukkoPera/WheelOfJoy/releases) rather than the current git version, as the latter might be under development and is not guaranteed to be working.

Every release is accompanied by its Bill Of Materials (BOM) file and any relevant notes about it, which you are recommended to read carefully.

## License
The WheelOfJoy documentation, including the design itself, is copyright &copy; SukkoPera 2019-2021 and is licensed under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License](https://creativecommons.org/licenses/by-nc-sa/4.0/).

This documentation is distributed *as is* and WITHOUT ANY EXPRESS OR IMPLIED WARRANTIES whatsoever with respect to its functionality, operability or use, including, without limitation, any implied warranties OF MERCHANTABILITY, SATISFACTORY QUALITY, FITNESS FOR A PARTICULAR PURPOSE or infringement. We expressly disclaim any liability whatsoever for any direct, indirect, consequential, incidental or special damages, including, without limitation, lost revenues, lost profits, losses resulting from business interruption or loss of data, regardless of the form of action or legal theory under which the liability may be asserted, even if advised of the possibility or likelihood of such damages.

## Support the Project
If you want to get some boards manufactured, you can get them from PCBWay through this link:

[![PCB from PCBWay](https://www.pcbway.com/project/img/images/frompcbway.png)](https://www.pcbway.com/project/shareproject/WheelOfJoy_Commodore_16_116_4_8_Player_Joystick_Adapter_059c7037.html)

You get my gratitude and cheap, professionally-made and good quality PCBs, I get some credit that will help with this and [other projects](https://www.pcbway.com/project/member/shareproject/?bmbid=41100). You won't even have to worry about the various PCB options, it's all pre-configured for you!

Also, if you still have to register, [you can use this link](https://www.pcbway.com/setinvite.aspx?inviteid=41100) to get some bonus initial credit (and yield me some more).

You can also buy me a coffee if you want:

<a href='https://ko-fi.com/L3L0U18L' target='_blank'><img height='36' style='border:0px;height:36px;' src='https://az743702.vo.msecnd.net/cdn/kofi2.png?v=2' border='0' alt='Buy Me a Coffee at ko-fi.com' /></a>

## Credits
* Thanks to Solder for being such a prolific hacked and a great inspiration :).
* 3D model of [DB-9](https://grabcad.com/library/d-sub-9-pin-male-1) and [DB-15 connector](https://grabcad.com/library/d-sub-15-pin-female-2-lines-1) by Alex K.
* Font used for port numbers is [Agreement Signature by wepfont](https://www.fontspace.com/a-agreement-signature-font-f52534) (800 DPI).
* Font used for the logo is [Senja Santuy by Zuzulgo](https://www.fontspace.com/senja-santuy-font-f74971) (450 DPI).
