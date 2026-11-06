# Changelog

## 0.3.0-beta.24

- Correct the factory mapping author from SolidSidemane to SolidSideman.

## 0.3.0-beta.23

- Use ReaPack's win64 platform id so Windows can see APC Console.

## 0.3.0-beta.22

- Align the published Windows x64 binary with 0.3.0-beta.22.

## 0.3.0-beta.21

- Ship Windows x64 in ReaPack with the same documents and factory mappings as macOS.

## 0.3.0-beta.20

- Show an ASCII x in the Active column instead of Unicode ballot marks.

## 0.3.0-beta.19

- Stop Library from reappearing when switching to Diagnostics on Windows.

## 0.3.0-beta.18

- Hide the Library page cleanly when switching to Diagnostics on Windows.

## 0.3.0-beta.17

- Keep the Windows Console library page parented to the panel so clicks respond.

## 0.3.0-beta.16

- Fit Windows Console and Faders to screen size with page scroll and less flicker.

## 0.3.0-beta.15

- Move GitHub Actions checkout and upload-artifact onto Node.js 24 runtimes.

## 0.3.0-beta.14

- Use a writable temp path for FX mode file tests on Windows.

## 0.3.0-beta.13

- Use the portable directory helper in the factory profile test on Windows.

## 0.3.0-beta.12

- Allow ListView text setters to compile under MSVC strict string const rules.

## 0.3.0-beta.11

- Fix Windows MSVC build for common controls, file mtime, and near macro clash.

## 0.3.0-beta.10

- Add portable filesystem helpers, Windows build scaffolding, and GitHub Actions CI.

## 0.3.0-beta.9

- Remove the Verbose checkbox from Diagnostics in the APC Console Panel.

## 0.3.0-beta.8

- Match factory Pages mappings to the live plug-in even when REAPER adds a VST3 UID.

## 0.3.0-beta.7

- Show every Pages fader in the Console panel, labeled by fader and page.

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
