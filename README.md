# APC Reaper Console

REAPER extension (`IReaperControlSurface`) for the Akai APC mini mk2, release `0.3.0-beta.1`.
The **APC Reaper Console** surface opens the MIDI ports chosen in REAPER's standard dialog, writes a diagnostic log, and maps tracks, the mixer, and FX as described below.

The version string lives in `kExtensionVersion` (`src/midi/apc_protocol_config.hpp`). The startup log, the surface description, and a scan report all use that string. The build does not ask git for a version.

## Release notes

### 0.3.0-beta.1

- Shareable profile import and export, with project overrides, personal templates, and a reserved bundled folder.
- Starter maps and a scan of installed plug-ins, including JSFX `potcfg` spans.
- APC faders window: swap, clear, assign, and save the personal template.
- REAPER's host-tail Bypass, Wet, and Delta stay off starter faders.
- A scan writes a JSON report and a text report under `APCReaperConsole/reports/`.
- Profile provenance is recorded for starter maps. Older profile files still load, and they are not rewritten just to add the new fields.

## Requirements

- macOS Apple Silicon
- CMake 3.21 or later
- Ninja
- Apple Clang (Xcode Command Line Tools)
- SDK submodules:

```sh
git submodule update --init --recursive
```

| Submodule | Repository |
| --- | --- |
| `third_party/reaper-sdk` | https://github.com/justinfrankel/reaper-sdk |
| `third_party/WDL` | https://github.com/justinfrankel/WDL |

`sdk/reaper_plugin.h` includes `../WDL/swell/swell.h` relative to the header's real path. Configure creates the link `third_party/reaper-sdk/WDL` → `../WDL/WDL`. It is not a submodule file: it is only the layout the SDK expects. On macOS the extension does not link SWELL (`SWELL_PROVIDED_BY_APP`, no Win32 calls).

## Configure and build

```sh
cmake --preset macos-arm64
cmake --build --preset macos-arm64
```

## Binary

`build/macos-arm64/reaper_apc_reaper_console-arm64.dylib`

REAPER loads only libraries whose name starts with `reaper_`. The `-arm64` suffix selects the Apple Silicon binary. The `lib` prefix is not added.

## Installation

The `install` target copies the dylib to:

`~/Library/Application Support/REAPER/UserPlugins`

The build does not run it. Run it only when you want to update REAPER:

```sh
cmake --install build/macos-arm64
```

Then restart REAPER.

## Distribution

One ReaPack package installs every component. In the repository it is **APC Console**, category **Control Surfaces**, author **SolidSideman**. The definition file is `Control Surfaces/APC Console.ext`. Its `@version` is the same string as `kExtensionVersion`. The repository name in `index.xml` is **APC Reaper Console**.

Import the repository from **Extensions > ReaPack > Import repositories**:

`https://github.com/fabiokamoto/APC-Reaper-Console/raw/main/index.xml`

Then install **APC Console** from **Extensions > ReaPack > Browse packages**. `0.3.0-beta.1` contains letters, so enable pre-releases for this package. The steps are in `docs/QUICK_START.md`.

Installing the package places:

- `UserPlugins/reaper_apc_reaper_console-arm64.dylib` on macOS Apple Silicon. This binary registers the actions and adds **Extensions > APC Reaper Console > Console panel**. That window shows **User Profile** at the top left and **Plugins Scan** with **Mapping Share** at the top right, then **Plugin List**. **Delete** sits at the bottom left of **Plugin List**.
- Factory mappings in `Data/APCReaperConsole/bundled/`. ReaPack's data directory is `Data/` inside the REAPER resource path. The extension reads that folder. A copy left in `APCReaperConsole/bundled/` by a source checkout is also read.
- `Data/APCReaperConsole/README.md`, `QUICK_START.md`, `MIDI_LED.md`, and `CHANGELOG.md`.

The package does not contain `APCReaperConsole/user/`. Installing or removing it does not change personal templates or REAPER projects. Restart REAPER after the install. The new dylib is loaded only then. The package does not add the control surface by itself: add **APC Reaper Console** once under **Preferences > Control/OSC/web**.

`packaging/write_reapack_index.py` writes `index.xml` from the package definition, the arm64 dylib in `Control Surfaces/`, the four documents, and the 422 factory mappings. Those files are published to the public repository https://github.com/fabiokamoto/APC-Reaper-Console. This source repository is https://github.com/fabiokamoto/APC-RC-SourceCode. The script does not create a git tag. `cmake --install` still copies only the dylib used for development. `packaging/install.sh` is the checkout installer exercised by the package test; the install path for another Mac is ReaPack.

## Adding the surface

REAPER 7.80 has no `Preferences > Control/OSC/web > Add` menu item. **Add** is a button inside the preferences window.

1. Quit REAPER if it was open while the file was copied.
2. Copy or install `reaper_apc_reaper_console-arm64.dylib` into UserPlugins.
3. Start REAPER.
4. Press **⌘,** or open **Options > Preferences**.
5. In the filter field at the top left, type `OSC`.
6. Select the **Control/OSC/web** page. That page is separate from **MIDI devices**.
7. In the right panel, press **Add**.
8. **APC Reaper Console** must appear in the **Control surface mode** menu.

The add dialog has two menus, **MIDI input** and **MIDI output**, as in the SDK CSurf examples. REAPER stores `0 0 <inDev> <outDev>`. `<none>` is index `-1` and does not open the port. Without that choice the config stays empty and the log shows `index -1`.

In the dialog, choose input and output among the APC ports (observed names are indicative: “Control” and “Notes”). Do not assume which pair is the right one: the log reports the index that opened. If a port is already enabled in **Preferences > MIDI devices** as a track input or output, REAPER can refuse it for the control surface (`CreateMIDIInput` / `CreateMIDIOutput` apply to devices that are not already open there).

The default does not light LEDs. Discovery turns on only with a token in the config string, or with `-DAPC_CONSOLE_DISCOVERY_MODE=N` (0 off, 1 input, 2 led_grid, 3 led_controls, 4 led_behaviors). The `discovery=` token wins over the constant. The MIDI dialog keeps the rest of the string when you press OK.

Quit REAPER, edit the `csurf_` line in `~/Library/Application Support/REAPER/reaper.ini`, then reopen REAPER. Input example, with no MIDI output:

`csurf_0=APCREAPERCONSOLE 0 0 8 6 discovery=input`

Narrow grid example (notes 0–15, not 0–127):

`csurf_0=APCREAPERCONSOLE 0 0 8 6 discovery=led_grid start_note=0 end_note=15 test_channel=7 test_velocity=5 on_ms=500 gap_ms=150`

A single note:

`csurf_0=APCREAPERCONSOLE 0 0 8 6 discovery=led_controls test_note=0 test_channel=7 test_velocity=5`

Behaviors on one note, without color names:

`csurf_0=APCREAPERCONSOLE 0 0 8 6 discovery=led_behaviors test_note=0`

To turn off LEDs the scanner lit: remove the surface, or put `discovery=off` back and restart. On close the surface sends Note On velocity 0 only for notes the scanner turned on. `discovery=off` sends nothing.

## Log

Every line starts with `[APCConsole]` and a level: `Error`, `Warning`, `Info`, or `MIDI`.

- File only: `<REAPER resource path>/apc-reaper-console.log`. On this install that is `~/Library/Application Support/REAPER/apc-reaper-console.log`. Opening a project does not open the REAPER console.
- `Error`, `Warning`, and `Info` are written while **Enable Logs** is on. `MIDI` lines are written only when **Verbose** is also on. An Info log does not receive the MIDI stream.
- **Diagnostics** in **APC Console Panel** has **Enable Logs**, the **Verbose** check under it, then **Show Logs**, **Export Logs**, and **Clear Logs**. The two checks are saved and used the next time REAPER starts. Until they are saved, a surface config that contains `verbose` still turns **Verbose** on. **Clear Logs** empties the log file and the recent lines.

On open, the version line contains `0.3.0-beta.1`, the architecture, the indices, and the `open`/`closed` state. The control-surface description shows the live input and output state: `open`, `missing`, `unset`, or `duplicate surface`.

## Input test

1. Add **APC Reaper Console** with an input port. Turn on **Verbose** in the **Diagnostics** tab when you need the MIDI lines. The surface config token `verbose` does the same until that check is saved.
2. Move a pad, a button, and a fader.
3. In the log, look for `MIDI IN` with `type` `NoteOn`, `NoteOff`, or `CC`.
4. Repeat on the other APC port if the first one produces no events.
5. Fill the table below with the message that was actually received.

## LED calibration

`led_calibration` is off by default. With no token, or with `led_calibration=off`, the pads follow track selection. Calibration does not start by itself. Keep `discovery=off`. Quit REAPER, edit `csurf_` in `reaper.ini`, and reopen.

1. Palette on the first three pads of the top row (56 off, 57 available, 58 selected; 59–63 off):

`csurf_0=APCREAPERCONSOLE 0 0 8 6 led_calibration=palette`

2. One MIDI channel per pad, fixed velocity (`cal_velocity`, default 5). Pads 56–63 use channels 1–8:

`csurf_0=APCREAPERCONSOLE 0 0 8 6 led_calibration=channels cal_velocity=5`

3. One velocity per pad, a single channel (`cal_channel` one-based, default 1). Velocities: 0, 1, 5, 21, 45, 72, 100, 127.

`csurf_0=APCREAPERCONSOLE 0 0 8 6 led_calibration=velocities cal_channel=1`

For each pad, write the observed color, brightness, and whether it blinks or pulses in the table in `docs/APC_MINI_MK2_HARDWARE_MAPPING.md`. A color is not confirmed until it is written there.

To return to track select:

`csurf_0=APCREAPERCONSOLE 0 0 8 6 led_calibration=off`

On close, only notes 56–63 are turned off. The pattern runs once and does not repeat on every `Run()`.

## Track select

With `discovery=off` (the default) the bottom-row pads, notes 0–7 from left to right, select the tracks in the window. The Master is not part of the bank. A pad past the existing tracks does not change the selection and writes `[TRACK] slot=N unavailable`. Notes 56–63 no longer select: at startup they are turned off once and are not driven again, except during LED calibration.

Prepare a project with at least eight tracks. In **Control/OSC/web** the surface must use indices that are still valid on this machine (in the last test: input 8, output 6, config `0 0 8 6`).

- Bottom pad, from the left: selects the eight tracks in view. One pad stays green; the other slots that have a track are medium red.
- Select one or more tracks with the mouse: the bottom-row LEDs follow the selection, including multi-selection.
- With fewer than eight tracks, pads without a track stay off.
- LED, bottom row, channel in the status nibble: off = `90` velocity 0; track present = `92` velocity 5 (medium red); track selected = `96` velocity 21 (full green). Blinking red (`97` velocity 5) is unused.

The window shows 8 tracks starting at `trackOffset` and does not follow the selected track by itself. The primary path is the bank in steps of 8: Left and Right without Shift. The last bank offset is `8 * floor((trackCount - 1) / 8)`. With 13 tracks, bank 1 is offset 0 (tracks 1–8) and bank 2 is offset 8 (tracks 9–13, slots 6–8 off). Shift+Left and Shift+Right scroll by one track, inside the same range `0 … maxBankOffset` (with 13 tracks, offset 0–8). Even at an offset that is not a multiple of 8, pads and faders stay `trackOffset + slot`. A slot without a track does not write. Every offset change, even by 1, releases every fader. Scene notes 112–119 do nothing and their LEDs stay off.

| Action | Offset | Tracks in view |
| --- | --- | --- |
| startup | 0 | 1–8 |
| Right, bank +8 | 8 | 9–13, slots 6–8 off |
| Left, bank −8 | 0 | 1–8 |
| Shift+Right from scene 0 | 1, then up to 8 | one track at a time, up to bank 2 |
| Shift+Left from offset 8 | 7, then down to 0 | one track at a time |

Left is green if the offset is greater than 0 (`90 6A 01`). Right is green if you can still move forward (`90 6B 01`). At the limit the arrow is off. Another Right on the last window does not change the offset. If the track count drops, the offset moves back inside the new maximum. A selected track outside the window lights no bottom-row pad.

## Solo, Mute, Record Arm

Solo and, in Volume and Pan, Mute act on the first selected normal track, in project order. The Master is never that target. If several tracks are selected, those LEDs follow only the first. With no track selected they write nothing and LEDs 113 and 115 stay off. In Device, Mute bypasses the focused plug-in. In Send, Mute bypasses the selected send. Clip Stop and Stop All Clips close plug-in windows; see the master-fader section.

| Note | Action | LED |
| --- | --- | --- |
| 112 | close the focused plug-in window | off |
| 113 | toggle Solo | solid green if `I_SOLO` is not 0 |
| 114 | Mute: track, focused FX, or selected send | solid green while that target is muted or bypassed |
| 115 | toggle Record Arm, or Record this fader in Device | solid green while armed |
| 119 | close every open plug-in window | off |

The LED is Note On on MIDI channel 1: velocity 0 off, velocity 1 solid green. Velocity 2 is the blink and is unused. The state is read back from REAPER, so a mute, solo, or arm made with the mouse, the TCP, or an action updates the LED. A solo that is already on (including in-place or safe) is turned off; from off, the toggle writes the standard solo, value 1.

Try it: mute a track in REAPER and select it, and the Mute button lights. Select an unmuted track and it goes off. Press Mute, Solo, and Rec Arm and check that the REAPER state and the LED change together. Deselect everything: the three LEDs go off and a press changes nothing.

## Volume and Pan

At startup the mode is Volume: button 100 is green and faders 1–8 control the volume of the eight tracks in view, with pickup. Pan (note 101) lights its LED, turns Volume off, and the same faders control the pan of those tracks, from −1 to +1, center near CC 64. Volume and Pan have separate pickup. Changing mode or offset releases all of them. Changing the selected track does not change the mode: if you are in Pan, you stay in Pan.

Volume, Pan, and Device show the same FX matrix for the eight visible tracks. The FX are those of the chain, in the order already used by `TrackFX_GetCount`: a lit pad is an existing FX, and pads past the last one stay off. There are no empty mixer FX slots. The faders do not follow the grid: in Volume they stay volumes, in Pan they stay pans. A valid FX pad opens the GUI (`TrackFX_Show`, showFlag 3) and switches to Device once; the Note On is not processed again and does not select the track. A pad with no FX opens nothing and does not change mode. `fxOffset` and the FX selection stay when you move between Volume, Pan, and Device. Up and Down without Shift scroll that same FX window; the LEDs light if you can still scroll. Shift+Up and Shift+Down in Volume and Pan do not scroll FX and do not change the parameter page.

Send (102) shows the mixer send slots, not the creation order. The list is `GetTrackNumSends(track, 0x10000000)`: an empty slot stays off and does not pull later sends up. A hardware output occupies the slot but the pad stays off and is not selected. Only a send to a track lights. Volume is written with `CSurf_OnSendVolumeChange(track, mixerSlot | 0x10000000, volume, false)`, so the fader follows the visual slot. `compact_send_index` is only in the log. If the send moves and the routing is unique, the selection follows and pickup re-arms; if the routing is ambiguous or the send disappears, the selection clears. An active send that is not selected is bright green: channel 7 (100%) velocity 21, `#00FF00`. The selected send, including a muted one, uses the selected plug-in color: channel 7 velocity 13, yellow `#FFFF00`. A muted send that is not selected stays the same green at 10%, channel 1 velocity 21. Clicking a send so that REAPER selects its envelope (`I_SEND_IDX` on `GetSelectedEnvelope`) switches to Send, brings that track into the eight columns, scrolls the mixer slot into the seven rows, and selects the pad. The same send held still does not force Send again after another mode button. A mixer click that does not select the send envelope is not reported by the SDK. `CSURF_EXT_SETSENDVOLUME` is only a level change. With `send_follow_diag=1` that change is logged and marked as not a selection.

Track follow is on (`follow_track=0` turns it off). If the first selected normal track in REAPER leaves the eight columns, the window goes to bank `(index / 8) * 8`, pickup re-arms, and the mode does not change. A track that is already visible, no selection, or the Master track does not move the window.

FX follow is on (`follow_fx=0` turns it off) and uses `GetTouchedOrFocusedFX` in mode 1, the current focus, not the last touched parameter (mode 0). A new focus on a normal-chain FX brings the track into view, switches to Device, scrolls `fxOffset` only if the FX is outside the seven rows, and selects the pad. It does not reopen the GUI. If the window stays open but loses focus (`parm` bit 0), that is not a new focus and the mode does not return to Device by itself. The same focus held still does not rewrite the mode: Volume pressed afterwards stays Volume until another focus arrives. Take FX, input FX, master FX, and FX inside containers are ignored.

Device (103) uses the same FX matrix as Volume and Pan. Each column is a track in the current window. The seven pads above the bottom row are that column's FX, in FX-chain order, from the top: first row notes 56–63, seventh row notes 8–15. The bottom row, notes 0–7, stays Track Select in every mode. A valid FX pad opens that plug-in's GUI and does not change the track selected in REAPER. If the pad is pressed from Volume or Pan, the surface switches to Device and re-arms the FX fader pickups before accepting CCs. The selection is remembered per column; the last pressed pad, or a new FX follow, is the fader context. A pad with no track or no FX stays off. The instance identity is the GUID from `TrackFX_GetFXGUID`. Before every read or write the GUID is checked again; if the instance only moved and the GUID is unique, the mapping follows it. If it is missing or not unique, the Device faders turn off and Learn is cancelled. Nothing is assigned automatically. Focus that the GUI just opened reports back to REAPER is not a second mode change and does not clear the page. A new FX focus, arriving from outside, still brings the surface to Device.

In Device, faders CC 48–55 write only the resolved slot of the current page. Precedence is: project instance override, then personal template, then shared profile. Assigning, clearing, or swapping a fader in APC Faders clears the project override on that fader, so the open plug-in uses the personal template without being removed from the chain. An explicitly empty slot blocks the levels below it. With no chosen profile and no assigned slot the fader stays still: it does not take the first eight parameters. Two compatible templates at the same level are not chosen by themselves. Each plug-in has one page of eight faders. The limit is 32 instances per project. Shift+Up and Shift+Down do not open another fader page. Up and Down without Shift keep scrolling the FX list. Changing FX, page, profile, or project re-arms the eight Device pickups. The Device fader value is written with `TrackFX_SetParam`. The scale starts from the center of `TrackFX_GetParamEx`. If the text REAPER shows for that parameter, read with `TrackFX_FormatParamValue`, is linear across a wide interval that starts at zero (20 Hz–20 kHz, 0–20 kHz, 0–1000 ms), the throw follows the text: the first step stays near the displayed minimum, not at one hundred-and-twenty-seventh of the raw 0–1 value. Text that is already logarithmic stays on the raw scale. A readout that runs from below zero to above zero with its middle near the center, such as −360° to +360°, stays linear on every plug-in, including every MeldaProduction parameter of that shape. The middle of the fader is zero and the positive end is the end of the travel. Learn, Assign, saving the fader arrangement, and a new plug-in scan store `linear` on that slot, so a later parameter with the same readout is recognized while it is mapped. Without usable text, a wide positive interval stays at a constant ratio and the others stay linear. An optional `scale` field on the profile (`normalized`, `linear`, `log`, `stepped`) wins over this heuristic; `auto` or a missing field leaves it as it is. A stepped parameter, from `TrackFX_GetParameterStepSizes`, snaps to that increment in the same units as `TrackFX_GetParamEx`. A step of 1 on a 0–100 slider moves one raw unit at a time, so the cursor keeps traveling through the throw. A parameter with `istoggle` from `TrackFX_GetParameterStepSizes` snaps to 0 below CC 64 and to 1 from 64 up, and after pickup writes only when that boundary is crossed. The APC mini mk2 has no touch sensor. The APC faders window has a **Rec** button between Clear and Project. It arms the selected fader of the focused plug-in instance, stored in the project as `fxrec.<guid>`. The label is red. While that fader is recording the button face is a faint red. Focusing another instance loads that instance's choice, which starts disarmed when none was stored. A missing value records nothing. Turning Rec on puts the track in Latch, so any parameter on that track can be recorded. Turning it off restores Trim/Read, or Read if that was the mode before. A move while Rec is off only calls `TrackFX_SetParam` after pickup.

Moving a Device fader selects that fader in the APC faders window. Selecting an FX parameter envelope in REAPER selects the fader mapped to that parameter, changing the focused FX or the page when the parameter is not on the faders currently shown. In Device, the side Rec Arm button (note 115, the same button that arms the track in Volume) toggles **Record this fader** for the selected fader. Pressing it again while that fader stays selected turns Rec off. The Rec Arm LED is on when the selected fader is armed. In Volume, Pan, and Send the button still arms the track.

While transport is playing or recording and Rec is on, the track is in Latch. Existing envelopes on that track are armed for the pass, except the track volume envelope, because Latch writes only armed envelopes. Envelopes that were not armed are returned to that state when record turns off. A volume lane opened by entering Latch is hidden again. No parameter envelope is created until a fader moves. A moved parameter keeps its last value until transport stops. Touch would return to reading as soon as the fader is still, and Write would replace the whole pass even when the fader does not move. The move ends `400` ms after the last CC of that fader (`fx_automation_timeout_ms`, clamped to 100–2000) by one `TrackFX_EndParamEdit`, which releases the fader; Latch then holds that value until stop. The same end runs when the FX, the page, the surface mode, or the project changes, and when the slot is no longer valid. One undo block (`UNDO_STATE_FX`) covers the pass. Stopped transport sets the parameter and does not write automation. No new LED is used.

The APC faders window and the APC Console Panel reopen at the last screen position (`window_apc_faders`, `window_apc_console`). If the panel has never been moved, it opens where FX mapping last was (`window_apc_mapping`). Changing the focused FX refreshes the fader text in the existing window.

Learn is Shift+Device, only with a valid active FX. The Device button blinks (channel 1, velocity 2, already verified on the side keys). At start, `TrackFX_GetNamedConfigParm(..., "last_touched")` of the active FX is snapshotted: that value, already present, is not assigned. A later touch is required, seen as a change of index or of normalized value, and it must belong to that FX. The same parameter touched again with no value change is not observable. If the plug-in paints its own knobs and does not report the touch, Learn follows the parameter value that was still and then moved. JS: AC Trem (Tukan) is one of these: Speed, Depth, Stereo, Spring, and Reverb assign as Frequency (Hz), Amount (dB), Stereo Seperation, Spring Type, and wet (%), which are the names REAPER lists. A value that is already moving when Learn starts, such as a meter, is not a touch. Then the first move of a fader CC 48–55 assigns the slot of the current page and does not write the parameter; that slot's pickup stays released. Learn writes to this instance. APC Faders saves the current arrangement with Project, or the personal template with Global. A Global save writes the User Profile name as the author on that personal template. If that name is empty, the author is Unknown User. After a successful save, a window says the new mapping was saved and closes with OK. The plug-in list refreshes and keeps the search text and the filters that are already on. Volume, Pan, and Send do not enter Learn. Shift+Pan during Learn does not switch to Pan and does not clear the page by itself: it prepares a block and writes the warning to the log.

“Project plugins”, in **Plugins Scan** on the console panel, reads the FX already inserted on tracks and on the master. For each new `fx_ident` it fills faders 1–8 with the plug-in's first parameters, in the order the plug-in lists them: fader 1 is the leftmost one on the APC. A parameter with no ident, a repeated ident, a meter, a bypass, an on/off button, a name that starts with DONT, a parameter named only Scaling, or Zoom and Zoom Gain stay out. The last three parameters stay out when they are named Bypass, Wet, and Delta, because those belong to REAPER's plug-in host. A button is also what `TrackFX_GetParameterStepSizes` reports as a toggle. A stepped parameter with fewer than ten positions is a selector: it is included only if faders are still free after the wide-scale parameters. From ten positions up, or if the step is continuous, it is included first. Buttons, bypass, and the names excluded above stay out in that second pass as well. What remains are parameters with a value scale. If there are fewer than eight, the later faders stay empty. REAPER does not report a knob's graphic position, so a control at the bottom left cannot be identified. When an FX is focused on the APC the “APC faders” window opens: eight faders side by side. An empty slot, and a slot whose parameter is named Not mapped, is a dense red-and-black texture and has no parameter name. An assigned slot has a yellow level and, under it, the parameter name. It stays visible even if the mapping window is closed. Drag one fader onto another to swap the parameters; if the destination is empty the parameter moves and the source slot stays empty. Click a fader to select it. Clear removes that parameter in this project. The menu lists plug-in parameters that are not already on a fader, and Assign puts the chosen one on the selected fader. Bypass, Wet, and Delta are not in that list. The swap applies immediately to this instance. “Project” writes the page into the instance. “Global” writes it into the personal template. A JSFX scan reads `name.potcfg` in the source and, if that minimum and maximum are tighter than the slider REAPER exposes, writes them on the slot (`span_min`, `span_max`). The fader throw uses that span: Key In and Key Out on the Tukan Deesser start at the knob minimum, about 1.50 kHz and 2.00 kHz. The step inside that span is the slider's own increment, so a step of 1 does not pin the cursor to the two ends. The fader's pickup echo uses that same span, so a move does not drop the latch and freeze the cursor until the fader returns. It does not open GUIs, does not change mode, and does not replace a personal template that is already present or an instance learn: the instance learn keeps winning over a later template. A plug-in that is not in the project has no readable parameters until it is loaded, so it is not part of this scan.

REAPER does not expose a version among the named config parms: for VST and VST3, `CFBundleShortVersionString` is read from the path inside `fx_ident`. An AU or a JS whose ident has no path stays “Version any”.

Saving to the instance writes the JSON document into the project (`SetProjExtState`, extname `APCReaperConsole`, key `fxjson.<guid>`). The old `v1` format stays on disk until that write succeeds. A slot missing from v1 becomes explicitly empty, so a new template does not fill it. Saving globally writes `GetResourcePath()/APCReaperConsole/user/<profile_id>.json`. The schema is 1; it becomes 2 if a slot has an explicit scale. An extension update does not overwrite those files. The parameter identifier is `TrackFX_GetParamIdent`. The global template is bound to `fx_ident` (`TrackFX_GetNamedConfigParm`), not to the instance GUID and not to the displayed name. Saving that personal template asks whether to apply it to every other installed format of that plug-in (VST, VST3, AU, and the other alias formats) or only to the current format. Yes applies it to all of those formats. No keeps it on the current format. Parameters are matched by name. A parameter that does not exist on a format leaves that fader unassigned there. JS and JSi stay on their own script. If the ident does not resolve, the slot does not write. **Extensions > APC Reaper Console > Console panel** opens one modeless window, not a dock, and it does not open on every FX change. **User Profile**, at the top left, stores your name. **Plugins Scan**, beside it, holds **Project Plugins auto-map**. **Mapping Share** holds **Import** and **Export**. **Export** opens a window listing the mappings whose author is the saved User Profile name, and writes the ones you check into one JSON file. **Plugin List** holds the filters and the profile list. **Faders**, to the right of that list, shows faders 1–8 and the parameter assigned to each. **Profile** acts on the selected mapping. Delete, disable, and import ask in a confirmation window. A mapping you save uses the Personal use license, and that field cannot be changed. The list and the fader table scroll on their own. When the page is taller than the window, the wheel and the trackpad scroll the page. Assign, clear, and save of a single fader are on APC Faders.

**Export** writes the mappings you check. It does not ask for new author or license fields and it does not copy a project GUID, a path, a project name, or a track name. A shareable JSON still uses schema 1, or 2 when a slot has an explicit scale. A file with schema 1 or 2 stays readable; an unknown schema is rejected and replaces nothing. The license on a shareable file is one of `CC0-1.0`, `CC-BY-4.0`, and `personal-use`. An older file that says `uso-personale` is read as `personal-use`. `verification` is `contributor-tested` only when a file already says the author tried it; otherwise it is `unverified`. That label is not local validation. An invalid slot, or one with no ident, is not written as assigned.

`contributor-tested` means the person who exported the file states that they tried it on their own plug-in. `maintainer-verified` would be a check by the person who publishes the extension, and an import cannot obtain it: if the JSON declares it, it is kept as `unverified`. Functional validation requires an install that has that plug-in. The automated tests check only the file structure.

**Import** in Profile library opens the chosen file and asks before writing. Yes imports it. If that `profile_id` is already imported, Yes keeps both and No replaces the imported file. A template in `user/` with the same id is not replaced; the window offers keep both or cancel. The write goes to `GetResourcePath()/APCReaperConsole/imports/<profile_id>.json`. The file name comes from the sanitized id: an id with `..` or a slash is not written. If the write fails, the in-memory library and the personal files stay as they were.

The library has three folders, all under `APCReaperConsole/`: `user/` holds personal templates and is not touched by import or by distributed profiles; `imports/` holds received files; `bundled/` is reserved for profiles a future release may install, and import does not write there. An import does not select the profile. Checking **Active** chooses that profile for its plug-in, and re-arms the eight Device pickups when that plug-in is in focus. It writes `choices.json`, not the project. A check on another plug-in is stored and used when that plug-in is focused. If there is more than one shared profile and there is no choice, that level stays unresolved and the status line says “Selection required.” Precedence stays instance, then personal template, then shared. The window state is Imported, Compatible, Validated, or Active. Without the plug-in the profile stays in the library, the state is Imported, and the faders do not write: “plug-in unavailable: local validation pending”. With the plug-in, each ident resolves with the same rules the mapping already uses; a slot that does not resolve stays invalid and does not write, and the others may. There is no download, account, or marketplace.

A community contribution can also be a profile package: one JSON document with a manifest and one or more profiles. It is optional. A single profile file still imports as before. The package carries the author, license, revision, changelog, tested plug-in, tested REAPER, and a contributor-tested claim. It does not download anything, and it cannot mark itself maintainer-verified. How to write one, which licenses are accepted, and how the two verification states differ is in `docs/FX_PROFILE_CONTRIBUTING.md`.

`docs/examples/fx-profile.example.json` is an example produced by the project's serializer. It is not a profile tried on a plug-in and it is not `maintainer-verified`.

## Profile provenance

A mapping has a storage class and, separately, a way it was produced. The on-disk key `schema` is the schema version (1, or 2 when a slot has an explicit scale). `schema_version` is accepted as the same integer. The schema was not bumped for these labels. `name` is the display name; `display_name` is accepted as an alias. `fx_version_tested` is the tested plug-in version; `tested_plugin_version` is an alias. `profile_version` is the author's own revision string. `author` and `license` are written when a save supplies them. The license tokens are exactly `CC0-1.0`, `CC-BY-4.0`, and `personal-use`. An older file that says `uso-personale` is read as `personal-use`. `tested_reaper_version` is written only when a save has one. There is no `created_at` or `updated_at`: a timestamp would change a byte-stable export, and the tests compare that export.

| | Project override | Personal template | Imported profile | Bundled profile |
| --- | --- | --- | --- | --- |
| `source_kind` | `project-instance` | `user-template` | `imported` | `bundled` |
| Where it lives | project extstate | `APCReaperConsole/user/` | `APCReaperConsole/imports/` | `APCReaperConsole/bundled/` |
| Precedence | first | second | shared, after the personal template | shared, after the personal template |

`origin` on a file is still the storage class: `user`, `bundled`, or `import`. When `source_kind` and `provenance` are absent they are inferred in memory (`user` becomes a manual learn, `import` stays an import, `bundled` becomes a bundled release) and the file is not rewritten. An explicit save can add them. A starter map writes `source_kind` `user-template`, `provenance` `starter-map`, and `verification` `unverified`.

`provenance` is `manual-learn`, `starter-map`, `import`, or `bundled-release`. `verification` is `unverified`, `contributor-tested`, `locally-validated`, or `maintainer-verified`. Import does not mark a profile `locally-validated` because the file was accepted. `maintainer-verified` on an imported file is stored as `unverified`. A plug-in that is not installed is not shown as verified.

## Profile library

APC Console Panel is not dockable. The plug-in list is a table with Type, Developer, Name, Version, Author, Active, and Profile. Version is the plug-in version the profile was tested with. Profile is the profile's own revision. It advances by one when that mapping is saved from APC Faders, not when a single fader changes. A fader edit that has not been saved is kept until REAPER quits, and the next launch opens the last saved mapping. Author is the name stored in the mapping file. Active is a checkbox: one mapping can be checked for each plug-in. Checking a row clears the check on the other mappings of that plug-in. Click a column title to sort by that column; click it again to reverse the order. The index is built after an import, export, installed scan, or a save from APC Faders, and when the window is open. A save keeps the search text and the filters that are already on. It is not rebuilt on every control-surface `Run()`.

The index reads three folders under `GetResourcePath()/APCReaperConsole/`, one level deep, and only `*.json` files:

| Category | Folder | Resolver |
| --- | --- | --- |
| Personal | `user/` | personal template, above a shared profile |
| Imported | `imports/` | shared layer, only after you select it when more than one matches |
| Bundled | `bundled/` | shared layer, same rule, and the file is read-only |

A file that does not parse stays in the list as a parse error. Two files with the same `profile_id` both stay visible. Each row is the plug-in as REAPER lists it, for example `VST: MTremolo (MeldaProduction)`. **Type**, **Developer**, and **Author** narrow that list. **Show Errors** keeps a mapping that did not parse, and any two mappings that share one profile id. **Show Inactive**, beside Show Errors, is off when the list opens, so only mappings with Active checked are shown. Turning it on shows every mapping. Search and the three menus still apply. JS and JSi developers are the first folder in `fx_ident`. Other formats use the developer written in parentheses. When two rows show the same plug-in, Author, Active, and Profile tell them apart. **Faders**, beside the list, has one row per fader: the fader number and the mapped parameter name. An empty Assign cell is an unassigned fader. Search is case-insensitive over the display name, `fx_ident`, original plug-in name, developer, author, `profile_id`, and origin. It does not use the network.

These states are separate:

| State | Meaning |
| --- | --- |
| Stored | The file parsed and is in the library. |
| Compatible | Its `fx_ident` matches the active FX type. |
| Locally validated | Every assigned parameter ident resolves on the current active instance. |
| Active | A checkbox in the list. Only one mapping of a plug-in can be checked. |
| Invalid | The profile or some assigned slots do not resolve. Invalid slots stay inactive. |

A profile can be Stored and not Compatible. It can be Compatible and not locally validated when the plug-in is absent or no instance is focused. `contributor-tested` is the author's claim. It is not local validation. A compatible profile is not activated by itself. The exception is the first launch on a computer that does not yet have `choices.json`: each factory mapping in `bundled/` is checked Active, one per plug-in. The next launch leaves those checks as they are, including any you clear. When several shared profiles match and none is selected, the window says **selection required** and Device faders do not pick one.

Checking **Active** stores the choice in `APCReaperConsole/choices.json` immediately, per plug-in, not per instance GUID. Only one mapping of that plug-in stays checked. Checking one clears the check on the other mappings of that plug-in. A plug-in that has a mapping always has one checked. Clearing the check on a mapping selects another version of that plug-in, the one whose file was written most recently. When the plug-in has only one mapping, that mapping stays checked and the Active cell does not change. Changing the choice of the focused plug-in rearms Device pickup.

**Import** asks in a window before it writes. Yes imports the file. If the profile is already imported, Yes keeps both and No replaces that import. A personal template with the same id is not replaced. **Export** opens a window that lists every mapping whose author is the saved User Profile name. The file contains the ones you check. Mappings saved under another name stay out of that window. It does not copy project GUIDs, paths, project names, or track names.

A Starter Map is a generated candidate with origin `starter-map` and verification `unverified`. You can inspect it, make it the active personal template, export it, or delete it after the confirmation window names the profile. The original file is not changed.

**Delete** asks in a window and then moves the file to `trash/user/` or `trash/imports/`. No leaves the file where it is. Bundled files cannot be deleted or edited in place. None of these operations change project instance overrides. If the deleted file was the active personal template, the mapping is resolved again and pickup is rearmed.

There is no remote library, account, or marketplace, and the browser does not choose among several compatible profiles for you.

A Starter Map is a generated candidate for the eight faders. It is not a musical preset and it is not maintainer-verified. The in-project **Project plugins** button reads FX already on tracks and on the master, up to 256 distinct `fx_ident` values and 1024 parameters on each plug-in, and it does not replace a personal template.

The 1,219 personal JSON profiles removed from `APCReaperConsole/user/` on 2 Oct 2026 are not recoverable from the extension. Instance mappings saved in the project are separate and still win over a personal template.

Changing `trackOffset` or the active project clears the FX window, the selections, and the fader context. Up (104) and Down (105) scroll every column together in Volume, Pan, and Device: the maximum offset is `max(0, longest FX chain among the eight visible tracks - 7)`. In Send they scroll only `sendOffset` and do not touch `fxOffset`. The Up and Down LEDs use the same green as Left and Right: on if the active browser's offset can still move. Shift+Up and Shift+Down do not open another fader page. Moving between Volume, Pan, and Device keeps the FX matrix and sends only the deltas; entering or leaving Send replaces all 56 notes. An available FX is channel 7 velocity 45. The selected FX pad is channel 7 velocity 13: yellow `#FFFF00` at 100%. A bypassed FX pad, including the selected one, is channel 1 velocity 45, that pale blue at 10%. In the master plug-in view the Volume button blinks (channel 1 velocity 2).

Manual check of the shared matrix:

1. In Volume, move a fader and check that the volume changes, while the pads show the chain FX.
2. In Pan, move a fader and check that the pan changes, with the same FX lit.
3. Press a lit FX pad from Volume, then go back to Pan and press another one.
4. Each press opens that FX's GUI and lights Device, turning Volume or Pan off.
5. The first fader move in Device must not jump the parameter: pickup is required. With no mapping the slot does not write.
6. Switch to Send: the 56 notes show only sends, including empty slots that stay off.
7. Go back to Volume or Pan: the FX return, not the sends.

## Volume fader

Track volume in the window only. The eight faders do not write pan, send, or FX. The faders are not motorized: there is no position feedback.

| Fader | CC | Slot |
| --- | --- | --- |
| 1–8 | 48–55 | 0–7, track `trackOffset + slot` |
| Master | 56 | master track volume in every mode |

REAPER volume is the linear gain from `GetTrackUIVolPan` (1.0 = 0 dB). The CC uses the CSurf example curve: dB with the `VAL2DB`/`DB2VAL` formulas, then `DB2SLIDER`/`SLIDER2DB` on 0–1000, then 0–127. The write is absolute `CSurf_OnVolumeChange` and `CSurf_SetSurfaceVolume` with this surface as `ignoresurf`, so the gesture does not re-arm itself. No undo is created for every CC.

Every fader starts released. It arms if the physical value is within 2 steps of the current volume, or if the movement crosses that value. On arm, the current value is written immediately. After an offset change, a track change in the slot, or a volume moved by mouse or automation, the fader returns to waiting and does not write until it arms again. A window change therefore does not jump the volume.

Try it with at least 13 tracks: at offset 0, faders 1–8 are tracks 1–8 after pickup. Right goes to offset 8: faders 1–5 are tracks 9–13, faders 6–8 do not write. Shift+Right or Shift+Left by one step releases the faders and reassigns `trackOffset + slot`. Move a fader far from the volume and check that the track does not move until you cross the level. Then change the volume with the mouse: the matching fader must cross the new level again before it writes.

CC 56 writes the master track from `GetMasterTrack` in Volume, Pan, Device, and Send, with the same pickup and the same `CSurf_OnVolumeChange` path. It is not one of the eight strips, and the APC faders window does not show it. Paging the bank does not select it. A project change, or a master volume moved with the mouse, releases it. Its own write does not. This version does not map it to an FX output or a send.

Shift+Volume shows the master-track plug-ins in the first pad column, turns the other columns off, and blinks the Volume button. Pressing one of those pads focuses that plug-in: the eight faders follow it the same way they follow a plug-in on a normal track, and Shift+Volume again returns the pads to the track grid. In Device, Mute bypasses the focused plug-in and the Mute LED stays on while it is bypassed. In Volume and Pan, Mute still mutes the selected track. In Send, Mute bypasses the selected send. Clip Stop (note 112) closes the floating window of the focused plug-in. Stop All Clips (note 119) closes every open plug-in window, including the master track.

## Discovery

With `discovery=input`, every Note On (velocity > 0) writes `[APCConsole][MAP] pressed note=…`. CCs write the first value and then only new minima or maxima. `t_ms` is the time since the surface started.

`led_grid` and `led_controls` advance in `Run()`: Note On, 500 ms, velocity 0, 150 ms, next note. The log is `[APCConsole][LED-SCAN]`. Channel 7 and velocity 5 are only the Akai protocol v1.0 suggestion, not a verified color. The table to fill is `docs/APC_MINI_MK2_HARDWARE_MAPPING.md`.

## Hardware verification table

| Control | Message received | Port | LED output tested | Note/CC confirmed |
| --- | --- | --- | --- | --- |
| Pad | | | | |
| Button | | | | |
| Fader | | | | |

## Duplicate or busy ports

- An index already open in **MIDI devices** makes `CreateMIDIInput` or `CreateMIDIOutput` fail. The log says `failed to open` and the description shows that side as `missing`.
- The APC input and output can be two different devices. Try the combinations and keep the one whose events appear in the log.
- Two surface instances on the same input or output index: the second stays closed and the description says `duplicate surface`. It does not send MIDI. When the first instance is removed, the next poll opens the ports for the second.

## Troubleshooting

**Extensions > APC Reaper Console** lists **APC Console Panel**.

The panel has two tabs:

- **Library** is the profile library.
- **Diagnostics** lists the input and output index and state, mode, bank offset, track, FX, send, page, pickup, mapping, and the last error in a table. Track names are not shown.

**Tests** holds **LEDs ON** / **LEDs OFF**, **Reset pickup states**, and **Export bug report**. **LEDs ON** sends the current LED state. **LEDs OFF** turns off the LEDs this surface owns and leaves them off until **LEDs ON**. **Reset pickup states** releases every fader, including the master. The fader writes again only after it crosses the current target.

**Enable Logs** writes the console log. **Verbose**, under that check, adds the MIDI lines. **Show Logs**, **Export Logs**, and **Clear Logs** sit on the next row, aligned with **Enable Logs**. **Show Logs** opens a window that follows the log. **Export Logs** saves the log file. **Clear Logs** empties it.

Unplug: within about half a second the matching port is closed, pickups are released, and the description changes to `missing`. Plug the same port name back in and the surface opens that port again, even if the index moved. It does not attach to a different name at the old index. Two devices with the same name stay closed.

## Recovery

These session changes release the faders that could otherwise write the previous target, drop a stale envelope pointer, and rebuild the LEDs:

- Project change. In-memory maps from the previous project are dropped. A project with no saved profile index loads no project maps.
- Undo and redo (`CSURF_EXT_RESET`) and track insert, delete, or reorder.
- Plug-in added, removed, or reordered. The focused plug-in is kept only when its GUID still matches one plug-in.
- Send added, removed, or reordered. A send is written only when its routing fingerprint still matches.
- MIDI port disconnect, reconnect, or a second surface on the same port.

A volume moved with the mouse still releases only that fader. Pickup tolerance is unchanged.

## Collecting a bug report

In the **Diagnostics** tab, choose **Export bug report** and save a local `.txt` file. The file contains the extension version, the REAPER version, `macOS`, the MIDI port indices and states, mode, offset, track number, FX, send, page, pickup, mapping ident, the last error, and the recent log lines that were admitted. **Export Logs** saves the log file itself.

The report does not include the project name, a project path, a home path, or track and destination labels. Those tokens are replaced with `[redacted]` if they were present in a log line. The REAPER log file on disk is separate and is not copied verbatim.

## Known limitations

- Disconnect and reconnect are detected from REAPER's MIDI device list, about twice a second. There is no hardware callback.
- Reconnect needs the port name captured while the port was open. A port that never opened at startup is not searched for later.
- Two ports with the same name are left closed.
- **LEDs OFF** stays dark until **LEDs ON**.
- Pickup is still a value match, not a touch sensor. Reset pickup does not move a REAPER control by itself.
- The bug report and the exported log are local files. They are not uploaded.
- **Enable Logs** and **Verbose** are per process. A saved check is used on the next launch. Until a check is saved, the last surface to open still applies the config token `verbose`.

## Manual test checklist

- [ ] `cmake --preset macos-arm64` finishes without errors.
- [ ] `cmake --build --preset macos-arm64` finishes without errors.
- [ ] `build/macos-arm64/reaper_apc_reaper_console-arm64.dylib` exists.
- [ ] `file` on the dylib reports `arm64`.
- [ ] `nm -gU` shows `ReaperPluginEntry`.
- [ ] After the copy into UserPlugins and a restart, **APC Reaper Console** is in the **Control surface mode** menu opened from **Options > Preferences > Control/OSC/web > Add**.
- [ ] Adding the surface shows no errors in **View > Show REAPER console**.
- [ ] Removing the surface from Preferences and restarting REAPER does not leave the extension in an error state.
