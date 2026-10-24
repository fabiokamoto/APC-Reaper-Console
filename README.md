# APC Reaper Console

APC Reaper Console `0.3.0-beta.5` is a REAPER control surface for the Akai APC mini mk2. It drives the mixer and the plug-ins on the tracks in view. This release is for macOS Apple Silicon and REAPER 7.80 or newer.

## Requirements

- macOS on Apple Silicon
- REAPER 7.80 or newer
- Akai APC mini mk2
- [ReaPack](https://reapack.com)

## Install

1. In REAPER, open **Extensions > ReaPack > Import repositories**.
2. Paste this address and confirm:

    `https://github.com/fabiokamoto/APC-Reaper-Console/raw/main/index.xml`

    Use that address. The GitHub page address, the one that contains `/blob/`, is not the package index.

3. Open **Extensions > ReaPack > Browse packages**.
4. Select the repository **APC Reaper Console**. The extension is **APC Console**, in **Control Surfaces**. The factory mappings are the packages in **Factory mappings**.
5. `0.3.0-beta.5` is a pre-release. In the package menu, enable pre-releases, then install **APC Console**. Install the packages in **Factory mappings** as well. Their versions are not pre-releases.
6. Quit REAPER and start it again. REAPER loads the new extension only after that restart.

**APC Console** installs the extension, this README, the quick start, the MIDI and LED manual, the user manual, and the changelog. The documents are in `~/Library/Application Support/REAPER/Data/APCReaperConsole/`. Each factory mapping is its own package and is installed in `Data/APCReaperConsole/bundled/`.

ReaPack downloads every file that belongs to a package whose version changed, and leaves every other installed package as it is. A factory mapping is downloaded again only when that file changed. Personal templates in `APCReaperConsole/user/` are not part of any package. Installing or updating does not change them or any REAPER project.

If factory mappings were already installed inside **APC Console**, the next update of that package removes them from it. In that same synchronization, install the new packages in **Factory mappings**. ReaPack writes the files back to the same folder. After that, an unchanged mapping stays on disk and is not downloaded again.

On a Mac that does not yet have `APCReaperConsole/choices.json`, one factory mapping per plug-in is checked **Active**.

## Configure

The package does not add the control surface by itself.

1. Open **Options > Preferences** (**⌘,**).
2. In the filter at the top left, type `OSC`.
3. Open **Control/OSC/web**. That page is separate from **MIDI devices**.
4. Press **Add**.
5. Choose **APC Reaper Console** as the control surface mode.
6. Choose the APC mini mk2 MIDI input and MIDI output. The port names are typically **Control** and **Notes**. If the first pair produces no response, try the other pair.
7. Leave those same ports disabled as track inputs and outputs under **Preferences > MIDI devices**. A port already open there cannot also be used by the control surface.

Then open **Extensions > APC Reaper Console > Console panel**.

**User Profile**, at the top left, is the name stored as the author when you save a personal mapping.

**Plugins Scan** holds **Project plugins**. **Mapping Share** holds **Import** and **Export**. **Plugin List** is the mapping library. **Delete** sits at the bottom left of that list. **Manual** sits on the same row, at the right edge of the window.

## Features

### Tracks and the mixer

The surface shows eight tracks at a time. The bottom row of pads selects those tracks. One selected pad stays green. Pads for the other tracks in view are red. A pad with no track stays off.

**Left** and **Right** move the window by eight tracks. **Shift+Left** and **Shift+Right** move it by one track. The arrows light while another step is available.

The mode buttons under the grid choose what the eight faders do:

| Mode | What the eight faders control |
| --- | --- |
| Volume | Volume of the eight tracks in view |
| Pan | Pan of those tracks |
| Send | Volume of the selected send |
| Device | The eight parameters of the active plug-in mapping |

The master fader controls the master track volume in every mode.

Faders are not motorized. A fader writes only after it reaches the current value, so a bank change does not jump the mix. **Shift** held with a Device fader moves a continuous parameter more slowly.

**Solo** toggles solo on the first selected track. **Mute** mutes that track in Volume and Pan, bypasses the focused plug-in in Device, and bypasses the selected send in Send. **Rec Arm** arms the track, except in Device, where it records the selected plug-in fader. **Clip Stop** closes the focused plug-in window. **Stop All Clips** closes every open plug-in window.

**Shift+Volume** shows the master-track plug-ins in the first column. Pressing one of those pads focuses that plug-in. **Shift+Volume** again returns to the track grid.

In Volume, Pan, and Device the pads above the bottom row show the plug-ins on the eight tracks. A lit pad opens that plug-in's window and switches to Device. **Up** and **Down** scroll that grid. In Send the same pads show the mixer sends of those tracks, including empty slots, which stay off.

If the selected track leaves the eight columns, the window follows it. Focusing a plug-in in REAPER brings that track into view and switches to Device.

### Plug-in mappings

In Device, each fader moves one parameter of the focused plug-in. The mapping used is the project override for that plug-in instance, then your personal template, then the shared factory or imported mapping. An empty slot does not take a parameter on its own.

**APC Faders** opens when a plug-in is focused from the APC. It shows the eight faders and the parameter on each. Drag one fader onto another to swap them. **Clear** removes the parameter from the selected fader. **Assign** puts a chosen parameter on the selected fader. Bypass, Wet, and Delta are not offered.

**Shift+Device** starts Learn on the focused plug-in. Move a control in the plug-in, then move a fader. That fader takes the control. The parameter is not written during the learn move.

**Save** keeps the arrangement. Until you save, a fader edit lasts for the session and is dropped when REAPER quits. Saving an existing mapping advances **Profile** by one. Assign, clear, or swap also drops a project override on that fader, so the open plug-in uses the personal template.

**Project plugins** reads the plug-ins already on tracks and on the master and fills a starter mapping for each one that does not already have a personal template. Meters, bypass, and the host Bypass, Wet, and Delta controls are left off those faders.

The plug-in list shows Type, Developer, Name, Version, Author, Active, and Profile. **Active** keeps one mapping checked for each plug-in. **Show Inactive** is off when the list opens, so only checked mappings are shown. **Show Errors** keeps a mapping that did not parse. Search and the Type, Developer, and Author filters narrow the list.

**Import** asks before it writes a shared mapping into `APCReaperConsole/imports/`. It does not replace a personal template with the same id. **Export** writes the mappings you check whose author is the saved User Profile name. The file does not contain the project name, a project path, or track names. Factory files in `bundled/` stay as installed. **Save** writes your copy under `APCReaperConsole/user/`. **Delete** asks, then moves a personal or imported file to the trash. A bundled file cannot be deleted from the list.

### Diagnostics

The **Diagnostics** tab shows the MIDI ports, the current mode, the track window, the focused plug-in, and the last error.

**Enable Logs** is off until you turn it on. It writes `apc-reaper-console.log` in the REAPER resource folder. **Verbose** adds the MIDI lines. **Show Logs**, **Export Logs**, and **Clear Logs** open, save, or empty that file. The two checks are kept for the next launch.

**LEDs ON** restores the current lights. **LEDs OFF** turns off the lights this surface owns until **LEDs ON**. **Reset pickup states** makes every fader wait until it matches the current value again. **Export bug report** saves a local text file with the version, the port state, and the recent log lines. It is not uploaded.

Unplugging the APC closes the matching port. Plugging the same port name back in opens it again.

## Remove

Quit REAPER. In **Extensions > ReaPack > Browse packages**, uninstall **APC Console**. The extension and the factory files that belong to the package are removed. Personal templates stay.

## Further reading

The same package installs three shorter documents next to this file:

- `QUICK_START.md` — install, the first mapping, and removal
- `MIDI_LED.md` — pad, button, and fader messages
- `APC_CONSOLE_MANUAL.pdf` — the user manual. **Manual** on the Library tab opens it in the default browser
- `CHANGELOG.md` — what changed in this version
