# MIDI and LEDs

Verified on an APC mini mk2. MIDI channel 1. Note On `0x90` velocity 127. Note Off `0x80` velocity 0. CC `0xB0`. The full pad map is `docs/APC_MINI_MK2_HARDWARE_MAPPING.md`.

| Control | Message |
| --- | --- |
| Pads | notes 0–63, bottom left is 0, top right is 63 |
| Buttons under the grid | notes 100–107 |
| Buttons on the right | notes 112–119 |
| Shift | note 122 |
| Faders 1–8 | CC 48–55 |
| Master | CC 56 |

| Mode | Note | Faders |
| --- | --- | --- |
| Volume | 100 | track volume |
| Pan | 101 | track pan |
| Send | 102 | selected send volume |
| Device | 103 | the eight slots of the current mapping page |

Shift+fader moves a continuous Device parameter at one tenth of the normal scale. There is no touch event. LEDs used by the surface are Note On, channel 1, 3, or 7, with the velocities recorded in the hardware mapping. Velocity 0 turns a pad off. The beta does not send SysEx.
