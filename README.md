# Vax Vox Designer

![Vax Vox Designer screenshot](assets/screenshot.png)

Vax Vox Designer is a single-page browser tool for designing VAX voice settings.

## GitHub Pages

This project is ready for GitHub Pages because the app entry point is named `index.html`.

Open `index.html` directly in a browser, or publish the repository with GitHub Pages enabled from the `main` branch.

## Version

v1.0

## Connecting Kraftor

1. Connect Kraftor to your computer with a USB data cable.
2. Open VAX Designer and click **Connect Kraftor**. Allow device access when the browser asks.
3. Edit a sentence and its voice settings, click **Audition draft**, then **Save to Kraftor**.

The app detects Kraftor automatically. If several Kraftors are attached, choose the one to use. Connection and memory status are shown separately: voice auditioning works without a memory module; saving and backups require initialized memory. Formatting erases existing memory and requires confirmation.

The main connection uses Web MIDI with SysEx over USB. There is no transport selector in the normal workflow.

## Earlier firmware and serial compatibility

Open **Older firmware / serial tools** and click **Connect serial** for earlier VAX firmware. Voice editing, auditioning, and the serial terminal remain available at 115200 baud. Memory operations require the main connection and current firmware. Disconnect serial before using the main connection.

Echo, the earlier serial Designer, and ordinary serial terminals remain supported. The firmware retains `/h`, `/i`, `/d`, `/o`, `/r`, `/v`, `/m`, plain-text speech, DECtalk phonetics, and `@` sentence mapping documented at https://www.polaxis.be/vax-serial-midi/.

Serial-loaded phrases temporarily take priority on their mapped MIDI notes, including when FRAM is present. They do not overwrite FRAM. Other notes continue to use saved slots; restarting clears the temporary phrases. Explicit Designer slot playback still plays the saved slot.

## Sentence editor and reset

The editor has one sentence box. Choose a voice and adjust its sliders; the
DECtalk voice parameters are assembled internally. Extra parameters already
stored in a slot are preserved when sliders are changed.

**Reset voice settings** restores the voice-design controls and speech rate
75, matching the firmware's starting rate. It keeps the chosen voice and the
sentence. Reset changes only the draft; audition or save explicitly.

## DECtalk native format

The same **Sentence** box accepts ordinary text, real native DECtalk commands and
bracketed phonemes. Click **DECtalk help** beside the box, or open **Help →
DECtalk native format**, for pronunciation, silence, singing, rate and voice-design
examples and links to the full guide. Audition before saving.

For example, `[:phoneme arpabet speak on][aa<500,25> aa<500,29> aa<1000,32>]`
sings “ah” on C4, E4 and G4. The first number is milliseconds; the second is a
DECtalk tone number, not the MIDI trigger note.

Native sentence text is preserved when auditioned and saved. Inline commands
follow the generated voice profile and can override sliders and live CC values;
the controls do not parse these overrides back from the sentence. Reset keeps
the sentence, including any inline commands. Syntax counts toward the text/byte
limits. `/r`, `/v` and `/d` are legacy serial terminal commands, not Sentence syntax.
Bulk paste supports one complete native phrase per line. Inline voice design
uses `[:dv ap 90 pr 0]`; bare `ap 90 pr 0` is text. The sliders generate their
own design command automatically.

Serial audition sends one complete native phrase, for example
`[:n0][:rate 75][:dv ap 90 pr 0]Hello.`, rather than `/v`, `/r`, `/d` setup
commands. One carriage return terminates it without an empty-line repeat.
This applies settings to that audition; it does not update the legacy terminal's
global `/v`, `/r`, `/d` settings for the separate temporary RAM phrase list.
The manual terminal still accepts those commands. The main USB MIDI connection
keeps its existing slot protocol and reconstructs the native phrase in firmware.

References: [Polaxis VAX examples](https://www.polaxis.be/vax-serial-midi/) and
the [DECtalk 5.01-E1 user guide](assets/EpsonDECtalk501.pdf)
(printed pages 44, 56–57). The PDF is bundled locally so Help also works
offline. Include `assets/EpsonDECtalk501.pdf` when sharing the Designer folder.
The guide is also available from [Polaxis](https://www.polaxis.be/download/dectalk-user-guide/).
This documentation is a GUI change; no firmware update is needed for native text passthrough.

## Bulk paste

Click **Bulk paste…** and paste one sentence per line, or the existing
`@one@two@three` format (starting with `@`). Choose the first slot and review
the destination preview, then click **Load drafts**. Blank lines are ignored.

Each destination keeps its existing voice settings. Empty slots use the current
editor settings. Select each slot afterwards to tune its voice and speech rate,
audition it, and save to Kraftor. Bulk paste does not send device commands or
write memory. Drafts are kept when connecting or reloading the memory bank.

The whole paste is rejected if any sentence exceeds the device limits or if
there are too few remaining slots; text is never silently truncated. **Undo
paste** restores the affected drafts until you make another edit or read/write
memory. Saved slots outside the pasted range are unaffected.

## MIDI CC labels and channel display

The voice controls display CC16 (voice, direct values 0-8), CC17 (speech rate),
CC18 (average pitch), CC19 (pitch range), CC20 (breathiness), CC21 (head size),
and CC22 (laryngealization). Hover a label for its value range. CC17-22 use MIDI
values 0-127 scaled to the slider range. CC121 resets live overrides.

Firmware 1.3 implements this map on the DIP-selected channel for USB and TRS.
Live CC overrides affect the next MIDI-note-triggered phrase without changing
FRAM. Designer audition uses the exact draft profile. Sliders edit drafts;
they do not send live CC messages automatically.

The GUI reads the active channel and four physical DIP positions from firmware,
with a read-only switch illustration. Click **Refresh** after changing switches;
restart Kraftor to apply a new channel. Older firmware that omits the information
is shown as unsupported/unknown, never as a guessed channel or switch position.

## Editor layout and Help

The selected slot's sentence, audition/save/play/erase actions, voice choices,
CC-labelled parameters and channel display are together in the left editor.
The right side is the complete slot list, with saved-memory print/backup tools.
Click a row or its slot number to edit it. Slot-number buttons also support
arrow keys, Enter and Space. **Help** explains the workflow, CC values, physical
switches, drafts, FRAM, legacy RAM and backups.

**Print saved slots** rereads FRAM and includes only populated saved records,
with exact stored settings, MIDI channel/note mapping and a CC legend. Browser
drafts and the legacy 12-phrase serial RAM list are excluded. Empty slots are
omitted. Temporary RAM phrases and live CC overrides are not reflected by the
printed inventory. Firmware older than 1.3 may not report the channel/CC map.
