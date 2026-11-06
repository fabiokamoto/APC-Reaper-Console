# Quick start

APC Reaper Console `0.3.0-beta.31` is a REAPER control surface for the Akai APC mini mk2 on macOS Apple Silicon and Windows x64.

## Install

1. In REAPER, open **Extensions > ReaPack > Import repositories**.
2. Paste this address and confirm:

    `https://github.com/fabiokamoto/APC-Reaper-Console/raw/main/index.xml`

3. Open **Extensions > ReaPack > Browse packages**.
4. Select the repository **APC Reaper Console**. The extension is **APC Console**, in **Control Surfaces**. The factory mappings are the packages in **Factory mappings**.
5. `0.3.0-beta.31` is a pre-release. In the package menu, enable pre-releases, then install **APC Console**. Install the packages in **Factory mappings** as well.
6. Quit REAPER and start it again. REAPER loads the new extension only after that restart.
7. Open **Options > Preferences**, filter `OSC`, and open **Control/OSC/web**.
8. Press **Add**, choose **APC Reaper Console**, and select the APC MIDI input and output.
9. Open **Extensions > APC Reaper Console**. On the Library tab, **Manual** opens the user manual in the default browser.

**APC Console** installs the platform extension (`reaper_apc_reaper_console-arm64.dylib` or `reaper_apc_reaper_console-x64.dll`), `README.md`, `QUICK_START.md`, `MIDI_LED.md`, `APC_CONSOLE_MANUAL.pdf`, `CHANGELOG.md`, and `fx-modes.txt`. The documents are in `Data/APCReaperConsole/` under the REAPER resource folder (`~/Library/Application Support/REAPER` on macOS, `%APPDATA%\REAPER` on Windows). Each factory mapping is its own package in `Data/APCReaperConsole/bundled/` on both platforms. An update downloads a mapping only when that file changed, and leaves the other mappings as they are. Personal templates in `APCReaperConsole/user/` are not in any package, and installing or updating does not change them or any REAPER project.

Removing **APC Console** removes the extension and those documents. Removing a package in **Factory mappings** removes that one mapping. Personal templates stay. ReaPack asks before it installs or removes a package.

On a computer that does not yet have `APCReaperConsole/choices.json`, one factory mapping per plug-in is checked **Active**. A plug-in with two factory files keeps both; **Show Inactive** lists the one that was not checked.

## First mapping

Focus a plug-in. Device faders CC 48–55 move the parameters of the active mapping. Assign, clear, or swap a fader in **APC Faders**, then press **Save** to keep that mapping. An edit that has not been saved lasts until REAPER quits.

Factory files in `bundled/` are not edited in place. **Save** writes a personal template.

## Remove

Quit REAPER. In **Extensions > ReaPack > Browse packages**, uninstall **APC Console**. Personal templates stay.
