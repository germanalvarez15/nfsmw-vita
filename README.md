![Need for Speed: Most Wanted for PS Vita](assets/header.jpg)

# Need for Speed: Most Wanted for PS Vita

An unofficial port of *Need for Speed: Most Wanted* (PS2, Black Edition, SLUS-21351) to the PS Vita.

> **This project does not include any game data.** You need your own legal copy of the game. The steps below read your disc and prepare the files for the Vita.

## Status

The current state of the port, with what is done and what is still missing, is kept up to date in this [status spreadsheet](https://docs.google.com/spreadsheets/d/1DiEtuSCBstFA1Gxpx7jiJBnwpX0CoKc_1XDqv4upIiQ/edit?usp=sharing).

## Reporting issues

Found a bug or a crash? Please open an issue in the [Issues](../../issues) tab of this repository. Include:

- Your Vita's firmware version and the release you installed.
- What you were doing when the problem happened, and whether it happens again.
- The log file `ux0:data/nfsmw/nfsmw.log`, if the game wrote one.
- After a crash, the newest `psp2core-*.psp2dmp` file in `ux0:data/`. If the game freezes, hold **L + R + Select** for 3 seconds: it closes and writes that file.

Check the status spreadsheet first: a known problem may already be listed there.

## What you need

| Item | Why |
| --- | --- |
| A PS Vita with homebrew enabled | To run the game. |
| Your copy of *Need for Speed: Most Wanted* Black Edition for PS2 (SLUS-21351), disc or ISO | The game data comes from it. |
| A computer with Python 3 | To run the data script. Windows, macOS or Linux. |
| ffmpeg (optional) | To prepare the movies. Without it the game runs without movies. |
| About 2 GB free on the computer and on the Vita | The game data is about 1.3 GB and the converted movies about 0.2 GB. |

## Why a script is needed

The PS2 disc does not store file names. The game finds each file by a number computed from its name. The Vita port reads normal files, so the script reads that index and writes each file under its real name. It also converts the movies, because the Vita only plays them in MP4 format.

The script does not download anything, does not change your disc, and writes only into the folder you choose.

## Installation

1. **Install the game** on the Vita: download `nfsmw.vpk` from the [Releases](../../releases) page and install it with VitaShell.
2. **Get the disc contents** on your computer. Extract the ISO with any tool (for example 7-Zip), or copy the disc's files. The folder must contain `SYSTEM.CNF` and the `NFS` folder.
3. **Install ffmpeg** (optional, for the movies). Check that it works by running `ffmpeg -version` in a terminal.
4. **Download this repository** and run the script from its folder:

   ```bash
   python3 scripts/extract-game-data.py "path/to/disc-folder" "path/to/game-data"
   ```

   On Windows, use `python` instead of `python3`.

   The script first copies the game files, then converts the movies one by one, showing its progress. The movies are the slow part: leave it running until it prints `Done`.

5. **Copy the contents of `game-data`** (the folders and files inside it, not the folder itself) to `ux0:data/nfsmw/` on the Vita. Create the `nfsmw` folder inside `data` if it does not exist.
6. **Start the game** from the LiveArea.

### Script options

| Command | What it does |
| --- | --- |
| `python3 scripts/extract-game-data.py DISC OUT` | Copies the game files and converts the movies. Needs ffmpeg for the movies. |
| `python3 scripts/extract-game-data.py DISC OUT --no-movies` | Copies the game files only. No ffmpeg needed. |

Running the script again is safe: it skips the files already in `OUT`, so you can run it again after installing ffmpeg to add the movies.

### Updating from an earlier version

Some versions need game files that earlier versions of the script did not copy. To update:

1. Download the latest `scripts/extract-game-data.py`.
2. Run it again with the same `OUT` folder. It only copies what is missing, so it takes a few seconds.
3. Copy the new files to `ux0:data/nfsmw/`. The release notes list them; for 1.2.0 they are `SOUND/SPEECH/COPSPEECH_EN.IDX` and `SOUND/STREAMS/NISAUDIO.IDX`.
4. Install the new `nfsmw.vpk`.

## Video settings

Open **Options > Video** from the main menu or the pause menu. The game saves the settings to `ux0:data/nfsmw/config.ini` when you leave the screen.

| Setting | Values | Default | What it does |
| --- | --- | --- | --- |
| Resolution | 480x272, 640x368, 720x408, 960x544 | 640x368 | The size the game draws at. The Vita scales it to the screen in hardware at no cost. Applies after a restart. |
| Frame Rate | 30, 60 | 30 | The frame rate limit. Applies at once. |
| MW Color Filter | On, Off | On | The yellow *Most Wanted* look of the PS2 game. Applies at once. |
| Car Light Glow | On, Off | Off | Draws headlights and brake lights at full brightness. Applies at once. |

**Resolution.** 960x544 is the screen's native resolution and the sharpest, but the hardest on the GPU, which limits the 3D scene. 720x408 and 640x368 are in between. 480x272 is the fastest but looks blurry. The menu shows *(restart)* until you restart the game.

**Frame Rate.** At 30 the game shows a new frame every second screen refresh, at 60 on every refresh. 60 is not guaranteed: a frame that takes longer than 16.6 ms is shown late, so the pace is less even. 30 is steady and uses less battery.

**MW Color Filter.** The tint, bloom and brightness curves of the PS2 game's visual treatment, drawn over the 3D view. Turning it off gives neutral colors without the yellow haze, and saves up to about 3 ms a frame: the game then skips that whole pass unless motion blur needs it.

**Car Light Glow.** With it on, headlight and brake light textures are drawn without shading, so a lit brake light looks lit even when the car is in shadow. With it off they are shaded like the rest of the car and look dimmer. It costs no performance.

### Overclocking

The game raises the CPU to 444 MHz, the Vita's standard maximum. The frame rate depends a lot on the CPU: an overclocking plugin that runs it at 500 MHz gives noticeably smoother races (in one race, 20-26 FPS at 500 MHz against 15-21 FPS at 444 MHz).

**Overclocking drains the battery faster and makes the Vita run warmer.** It is optional and at your own risk. It does not fix crashes or freezes, so please report those as usual.

## Using your PS2 save

You can continue a career from the PS2 game. Only saves from the **Black Edition (NTSC-U, SLUS-21351)** work, the same version the port needs; saves from the original release, the Greatest Hits, PAL or other consoles are refused.

1. Get the save out of the memory card in any of these forms:
   - A **.max** (Max Drive) or **.psu** (uLaunchELF, myMC) export, like the ones on save sharing sites.
   - The whole **`BASLUS-21351-<NAME>`** folder, copied from a real memory card with uLaunchELF or from a PCSX2 folder memory card.
   - The profile file inside that folder (also called `BASLUS-21351-<NAME>`, 62,689 bytes).
2. Copy it as it is into `ux0:data/nfsmw/save/`. Do not rename it.
3. Start the game. It converts the save into a profile named after the one on the PS2 and moves what you copied to `ux0:data/nfsmw/save/ps2-original/`.

If the profile does not appear, what you copied is in `ux0:data/nfsmw/save/ps2-not-imported/` and the log (`ux0:data/nfsmw/nfsmw.log`) has a `[memcard]` line with the reason: another version of the game, a damaged file, or a profile with the same name already on the Vita (rename or delete that one first, then move the save back to `save/`).

### Taking your Vita save back to the PS2

The game keeps a PS2 copy of every profile, updated each time it saves: `ux0:data/nfsmw/save/ps2-export/BASLUS-21351-<NAME>.psu`. It is the save folder the PS2 game makes, icon included, for the same Black Edition (SLUS-21351).

- **Real PS2:** copy the `.psu` to a USB stick and paste it onto the memory card with uLaunchELF (it imports `.psu` files).
- **PCSX2:** import it into the `.ps2` memory card file with myMC or myMC+.

## What the script does, step by step

- Checks that the disc is *Need for Speed: Most Wanted* Black Edition (SLUS-21351). Any other disc is refused.
- Reads the disc's file index and copies the 1232 game files, byte for byte, into `OUT`.
- Converts the 34 movies with ffmpeg, to MP4 files in `OUT/MOVIES`. The settings match the Vita's video decoder.
- Writes only into `OUT`. It has no network access.

## Troubleshooting

- **"SYSTEM.CNF not found" or "not the root of a PS2 disc":** the folder you gave is not the root of the disc. Choose the folder that contains `SYSTEM.CNF` and `NFS`.
- **"This is not Need for Speed: Most Wanted Black Edition":** the disc is another game or another version.
- **"ffmpeg was not found":** install ffmpeg, or use `--no-movies`.
- **The game runs without movies:** the movies were not converted. Install ffmpeg and run the script again.
- **The game shows an error about missing data:** check the log at `ux0:data/nfsmw/nfsmw.log` and confirm that the `game-data` folder was copied in full.
- **The game warns that sound files are missing:** some files did not reach `ux0:data/nfsmw/`. Run the latest script again with the same output folder and copy the `game-data` folder again. Without those files the game crashed after the race against Bull.

## Legal notice

This project is not affiliated with or endorsed by Electronic Arts. *Need for Speed* and *Most Wanted* are trademarks of their respective owners. This project does not distribute game data: you need your own legal copy to use it.

## Credits

- [VitaSDK](https://vitasdk.org) and [vitaGL](https://github.com/Rinnegatamante/vitaGL): toolchain and renderer.
- To [dbalatoni13 decompilation](https://github.com/dbalatoni13/nfsmw) which served as starting point for the decompilation for this port. 
