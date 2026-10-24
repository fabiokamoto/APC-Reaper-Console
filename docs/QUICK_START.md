# Quick start

APC Reaper Console `0.3.0-beta.2` is a REAPER control surface for the Akai APC mini mk2 on macOS Apple Silicon.

## Install

1. In REAPER, open **Extensions > ReaPack > Import repositories**.
2. Paste this address and confirm:

    `https://github.com/fabiokamoto/APC-Reaper-Console/raw/main/index.xml`

3. Open **Extensions > ReaPack > Browse packages**.
4. Select the repository **APC Reaper Console**. The package is **APC Console**, in **Control Surfaces**.
5. `0.3.0-beta.2` is a pre-release. In the package menu, enable pre-releases, then install **APC Console**.
6. Quit REAPER and start it again. REAPER loads the new extension only after that restart.
7. Open **Options > Preferences**, filter `OSC`, and open **Control/OSC/web**.
8. Press **Add**, choose **APC Reaper Console**, and select the APC MIDI input and output.
9. Open **Extensions > APC Reaper Console**. On the Library tab, **Manual** opens the user manual in the default browser.

The package installs the arm64 extension, the factory mappings, `README.md`, `QUICK_START.md`, `MIDI_LED.md`, and `CHANGELOG.md`. The documents are in `~/Library/Application Support/REAPER/Data/APCReaperConsole/`. Factory mappings are in `Data/APCReaperConsole/bundled/`. Personal templates in `APCReaperConsole/user/` are not in the package, and installing it does not change them or any REAPER project.

Removing the package in ReaPack removes the extension and the factory files that belong to the package. Personal templates stay. ReaPack asks before it installs or removes a package.

On a Mac that does not yet have `APCReaperConsole/choices.json`, one factory mapping per plug-in is checked **Active**. A plug-in with two factory files keeps both; **Show Inactive** lists the one that was not checked.

## First mapping

Focus a plug-in. Device faders CC 48–55 move the parameters of the active mapping. Assign, clear, or swap a fader in **APC Faders**, then press **Save** to keep that mapping. An edit that has not been saved lasts until REAPER quits.

Factory files in `bundled/` are not edited in place. **Save** writes a personal template.

## Remove

Quit REAPER. In **Extensions > ReaPack > Browse packages**, uninstall **APC Console**. Personal templates stay.
