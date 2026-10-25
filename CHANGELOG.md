# Changelog

## 0.3.0-beta.6

- Restore Pages and Parametric EQ for the factory plug-ins that were prepared that way.

## 0.3.0-beta.5

- Name SolidSideman as the author of the factory mappings that were labeled starter.

## 0.3.0-beta.4

- The Console panel lists factory mappings installed by ReaPack even when no control surface is configured.

## 0.3.0-beta.3

- Each factory mapping is its own ReaPack package, so an update downloads only the mapping that changed.

## 0.3.0-beta.2

- Enable Logs stays off until you turn it on, and Manual sits on the same row as Delete at the right edge of the Library tab.

## 0.3.0-beta.1

Beta for macOS Apple Silicon, REAPER 7.80 or newer, and the Akai APC mini mk2.

- ReaPack installs this beta from the git repository index. The package contains the arm64 extension, the factory mappings, the README, the quick start, the MIDI and LED manual, the user manual, and this changelog.
- **Manual**, on the Library tab of APC Console Panel, opens the user manual in the default browser.
- Factory mappings are the mappings checked Active in the maintainer library. Each shipped copy has profile version 1. The package does not write `user/`.
- A fader edit stays in the session until **Save**. Profile advances by one on that save.
- Assign, clear, or swap in **APC Faders** clears a project override on that fader so the open plug-in uses the personal template.
- The plug-in list opens on mappings with **Active** checked. **Show Inactive** shows the rest. A plug-in that has a mapping keeps one of them checked.
- Editing the active map, including a factory map, stays on that file. **Pin version** is what makes a separate map for one plug-in version.
