# TO8 Tracker

**A 4-channel ProTracker MOD player for the Thomson TO8 — 8KHZ 6-BIT DAC.**

TO8 Tracker brings Amiga-style tracker music to the original **Motorola 6809E at 1 MHz**, using the Thomson TO8's built-in 6-bit sound output. It mixes four sampled voices entirely in software at **8,000 samples per second**.

Hero Quest : https://youtu.be/0ES_-IJTEjo?si=_q2MIRcgj7B4R1mH

SuperCars 2 : https://youtu.be/Hrf9h6qHnBw?si=fBLXmeHAEY5jIT1G

Lotus2 : https://youtu.be/qMdo7bRqxaQ?si=Vxo4dsZCYlbDUMJ3

Démo Enigma : https://youtu.be/ScSxWaBqgYs?si=j7X0vLFYGU0gFo8q

Cannon Fodder : https://youtu.be/MHNWjYhJxBw?si=xhJ4t-gJwTvVDtlQ

Red Sector : https://youtu.be/teMMCTpapRQ?si=2LSFIUDssJHj41Sw


This repository distributes **ready-to-run releases**, not the assembler or build-tool sources.

## Download

**[Download the latest release](../../releases/latest)**

Download the **SDDrive disk image** listed under **Assets**. The automatically generated GitHub **Source code (zip/tar.gz)** links are snapshots of this *distribution repository*; they are **not** the player download.

## Highlights

- Four ProTracker channels mixed in software, with 8 kHz / 6-bit DAC output.
- Runs on the original Thomson TO8 and TO8D with 256k RAM extension.
- Native bootloader and an assembly-language user interface.
- On-screen MOD browser, instrument information, song/pattern progress and playback status.
- Play/pause, module navigation, loop modes and automatic playback of successive modules.
- Support for selected ProTracker effects, including portamento, vibrato, volume slides and pattern breaks.
- PAL Amiga-aware sample resampling to preserve the intended **sample pitch and playback speed**. This is particularly important for tunes that chain instruments, such as *Cannon Fodder*.
- Compressed program distribution: the native loader expands the packed program before playback,.

## Running it

### In DCMOTO

1. Download the **SDDrive disk image** from the latest release.
2. Select the **Thomson TO8** machine in [DCMOTO](https://dcmoto.pages-perso.free.fr/emulateur/index.html).
3. Enable the **SDDRIVE** extension.
4. Enable the **256 KB RAM extension**.
5. Load the SDDrive disk image and boot the emulated TO8.
6. Use the on-screen file browser to select a compatible MOD file.

### On a real Thomson TO8

Use the released disk image with a **compatible SDDrive interface** connected to your Thomson TO8. The emulator configuration above also uses an additional 256 KB of RAM; match that memory configuration on real hardware. Boot from SDDrive and select a MOD in the file browser.

The release is distributed in **SDDrive disk-image format**; it is not intended to be copied directly to a conventional floppy disk.

Add your own MOD music

The MOD files included in the release disk images were **copied as-is**, without modifying or converting the original modules, using **[DCFDUTIL](https://dcmoto.pages-perso.free.fr/emulateur/index.html)** from the DCMOTO website. This utility can import and export files in Thomson disk images, including the `.sd` format. **It cannot yet delete files from the disk image**: deletions must be performed through the **TO8 monitor**.

**You are encouraged to replace the supplied music with your own selection.** There are thousands of ProTracker modules freely available to discover online:

- **[Modland — ProTracker archive](https://ftp.modland.com/pub/modules/Protracker/)** — a direct archive of ProTracker modules, organized by artist.
- **[The Mod Archive](https://modarchive.org/)** — a large searchable tracker-music collection; look for four-channel ProTracker `.MOD` files.
- **[Amiga Music Preservation](https://amp.dascene.net/)** — Amiga music and composer archive with module search.

To customize a disk image:

1. **Keep a backup** of the original `.sd` image.
2. Boot the disk in **DCMOTO or on a real TO8**, then use the **TO8 monitor** to delete the supplied MOD music files you want to replace.
3. **Keep the boot sector and player system files intact.** Do not delete them.
4. Open the resulting `.sd` image with **DCFDUTIL**, import compatible four-channel ProTracker modules, and save the image. DCFDUTIL is used for importing files, **not deleting them**.
5. Boot the updated image using the DCMOTO configuration above, or with a real TO8 and SDDrive.

For the best results, choose MODs using **125 BPM** and **speed 4 or higher**. Speed 3 has limitations; other tempos and unsupported effects may not play correctly. Each module must also fit the TO8's memory constraints (see [MOD compatibility](#mod-compatibility)).

## Controls

Keys are contextual: the browser and player screens do not always assign them the same action.

| Key | Action |
| --- | --- |
| **Arrow keys** | Navigate the browser or song controls |
| **Enter / Space** | Start playback or play/pause, depending on the screen |
| **N / P** | Next / previous module during playback |
| **P** (browser) | **Play All** |
| **L** | Change loop mode |
| **A** | Toggle autoplay |
| **H** | Help |
| **E** (help) | Exit help |
| **M** (player) | Return to the module browser |

## MOD compatibility

TO8 Tracker targets **standard four-channel ProTracker MOD files** under the memory and timing constraints of the TO8.

- **Tempo:** playback is designed around **125 BPM**, with a 50 Hz tracker tick. Other BPM values are not reproduced at their original tempo.
- **Speed:** normal support at speed 4 and above; speed 3 is supported in a reduced mode, with some effects or pattern advancement unavailable.
- **Effects:** a subset of ProTracker commands is implemented. Vibrato support is constrained by the real-time budget; simultaneous pitch/volume effects may be simplified.
- **Size and samples:** large modules and instrument data may exceed available banked memory.
- **Audio quality:** four channels are reduced to an 8 kHz, 6-bit output. High notes and aggressive sample decimation can cause audible aliasing compared with the Amiga original.

Not every MOD will play perfectly. The goal is faithful musical timing and pitch for supported material within the TO8's limits.

## What's new in v2.0

Compared with v1.4, which already used the same core playback architecture:

- PAL-aware resampling for correct Amiga sample pitch and timing.
- Vibrato effect support.
- Support for speed 3 and speed 4 modules, with the limitations noted above.
- New assembly-language interface and native assembly-language bootloader.
- Compressed, unified program distribution.

See the [Releases](../../releases) page for version-specific downloads and changes.

## About this repository

This is the **public binary distribution repository** for TO8 Tracker. It contains release artifacts and user-facing documentation. The ASM sources, Python build tools and development files are maintained separately and are not part of this repository.

ProTracker MOD music may be subject to its original authors' rights. Please use and share modules in accordance with their respective permissions.
