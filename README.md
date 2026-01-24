# Linux Sim Launcher

A script to launch the Windows version of Opentrack, Simhub, Lookpilot and an AC SHM Bridge along with a steam game/app in the same prefix and with the same proton version.

## Dependencies

- **p7zip** needed for `7z` command
- **python3** needed for the launcher

## Usage

Open a terminal and copy paste the following command to install it:

```bash
# Root option: easier but discouraged
$ sudo wget https://raw.githubusercontent.com/giantorth/linux-sim-launcher/master/sim-launcher -O /usr/local/bin/sim-launcher && sudo chmod +x /usr/local/bin/sim-launcher

# Non-root option: not as easy but recommended
$ mkdir -p ~/.local/bin && wget https://raw.githubusercontent.com/giantorth/linux-sim-launcher/master/sim-launcher -O ~/.local/bin/sim-launcher && chmod +x ~/.local/bin/sim-launcher
```

Then in Steam, right-click the game you want to use this launcher with, click on `Properties` and in the `LAUNCH OPTIONS` field, depending on how you installed the script, type one of the following commands:

```bash
# If you installed the script with the root option:
sim-launcher %command%

# If you installed the script with the non-root option:
~/.local/bin/sim-launcher %command%
```

## Folders

The launcher creates a the following folder structure in your `~/.local/share` 

```bash
./sim-launcher
├── install         # opentrack portable Windows version install folder
├── scripts         # launch scripts (.bat files) for each game
└── version.txt     # stores the current installed version of opentrack (used for auto-updating opentrack)
```
