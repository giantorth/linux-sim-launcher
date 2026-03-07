Linux Sim Launcher
==================

A Python-based utility to synchronize the launch and cleanup of Windows-based simulation tools (**Opentrack, SimHub, LookPilot**) alongside Steam games. It ensures these tools run in the correct Proton prefix and provides an **Assetto Corsa SHM Bridge** for shared memory support on Linux.  This solves the need to run SimHub in a single prefix to simplify licensing and configuration instead of installing it in every game prefix.

You can find me on the [Sim Racing on Linux](https://simracingonlinux.com/) [discord](https://discord.simracingonlinux.com/) if you have any questions.

Key Features
------------

*   **Auto-updating Opentrack:** Automatically fetches and extracts the latest portable Windows version.

*   **SimHub Integration:** Launches SimHub into its own specific Steam AppID prefix using protontricks. The AppID is auto-detected from Steam's `shortcuts.vdf` -- no manual configuration needed.

*   **Monocoque**: Launch Monocoque with the game.

*   **AC Bridge:** Supports Assetto Corsa shared memory bridging (Native + Proton components).

*   **PC2 Bridge:** Supports Automobilista 2/Project Cars 2 shared memory bridging (Native + Proton components).

*   **LookPilot Support:** Toggle-able launch for LookPilot (AppID 3326890).

*   **Fully Kiosk Browser Integration:** Automatically turn tablet screens on/off via Fully Kiosk Browser REST API.

*   **Flatpak Support:** Automatically detects and works with Flatpak Steam installations.

*   **Automatic Cleanup:** When the main game closes, the script automatically kills all associated .exe processes and native bridges.


Dependencies
------------

*   **p7zip** (installed as 7z or 7za) - Required for extracting Opentrack.

*   **python3** - The core runner.

*   **python-vdf** - Required for Steam library and shortcut discovery.

*   **python-requests** - Required for Fully Kiosk Browser integration.

*   **protontricks** - Required for launching SimHub and the Proton Bridge.

*   **git, make, mingw-w64-gcc** - Required for automatically building simshmbridge (only when using --acbridge or --pc2bridge flags). Package names may vary by distribution: `mingw-w64-gcc` (Arch/Pacman) or `gcc-mingw-w64` (Debian/Ubuntu/apt).

*   **wget** - For installation.

Installation
------------

* Open a terminal and choose one of the following methods to install this script:

```bash
# Recommended: Local Install
$ mkdir -p ~/.local/bin && wget https://raw.githubusercontent.com/giantorth/linux-sim-launcher/master/sim-launcher -O ~/.local/bin/sim-launcher && chmod +x ~/.local/bin/sim-launcher
# Alternative: System-wide (Requires sudo)
$ sudo wget https://raw.githubusercontent.com/giantorth/linux-sim-launcher/master/sim-launcher -O /usr/local/bin/sim-launcher && sudo chmod +x /usr/local/bin/sim-launcher
```

* **Note:** [simshmbridge](https://github.com/giantorth/simshmbridge) is now automatically cloned and built when using `--acbridge` or `--pc2bridge` flags for the first time. Ensure you have the required build dependencies installed (git, make, mingw-w64-gcc).

* If you don't have it, install protontricks from your distro's package repo.

* Install simhub in its own Steam prefix:

    1. Add the SimHub installer to Steam as a **non-steam game**
    2. Edit the properties to set compatability options to a GE-Proton (better dotnet compatability)
    3. Run the SimHub installer and **make sure to uncheck** "Install Microsoft redistributables" and "Install USB display drivers" (they don't work on linux)
    4. Install dotnet48 in this prefix (the AppID is auto-detected, but you can find it by adding a desktop shortcut in Steam and inspecting its properties)

    ```bash
    $ WINEPREFIX=~/.steam/steam/steamapps/compatdata/ID_OF_SIMHUB/pfx winetricks -q --force dotnet48
    ```
    5. Edit the properties in Steam and browse to the correct executable (be sure to add quotes if Steam doesnt) example path: `"/home/USERNAME/.steam/steam/steamapps/compatdata/ID_OF_SIMHUB/pfx/drive_c/Program Files (x86)/SimHub/SimHubWPF.exe"`
    6. Ensure SimHub launches correctly from Steam

Potential Issues
----------------
* Some games require additional configuration to support SimHub.  Normally SimHub would configure this automatically for you but is unable to when running in its own prefix.  Either look up instructions on how to manually configure the game, or follow [these directions](https://gist.github.com/srlemke/617fe318ea26fed4cbd2edaec9209c86) to install a demo copy in a game's prefix so it is able to auto-configure the game for you.
* Simhub seemingly struggles to launch minimized if the game is already running in full screen.  Alt-tab until SimHub has launched.
* If the game exits abnormally, you may have leftover .exe programs running in your prefixes that must be killed manually


Usage in Steam
--------------

1.  Right-click your desired game in Steam -> **Properties**.

2.  In the **Launch Options** field, enter the command below.


### Basic Usage

`  ~/.local/bin/sim-launcher %command%   `

Or if you installed it for all users:

`  sim-launcher %command%   `


## Script flags

You can append flags to the launcher to enable specific tools:

| Flag | Description |
|---|---|
| --opentrack | Download (if needed) and launch Opentrack. |
| --simhub | Launch SimHub from its own Proton prefix. |
| --simhub-same-prefix | Launch Simhub from the same prefix as the game, must already be installed. |
| --monocoque | Launch Monocoque. |
| --acbridge | Launch the Assetto Corsa (AC/ACE/ACR) Shared Memory Bridge. |
| --pc2bridge | Launch the Project Cars 2 (Automobilista 2) Shared Memory Bridge. |
| --lookpilot | Launch LookPilot via Steam. |
| --kiosk-ip | Set the IP address of a Fully Kiosk Browser for automatic screen sleep/wake. |
| --kiosk-pw | Set the admin password of the Fully Kiosk Browser (required with --kiosk-ip). |
| --kiosk-port | Set the port for Fully Kiosk Browser (Default: 2323). |
| --debug | Enable verbose logging to file in ~/.local/share/sim-launcher. |
| --dry-run | Show what would be launched without executing. |
| --clean-shm | Clean up shared memory files after AC bridge exits. |
| --version | Show version number and exit. |

### Optional overrides

These flags are rarely needed since the launcher auto-detects SimHub's AppID from Steam's `shortcuts.vdf` and resolves paths via Steam library discovery.

| Flag | Description |
|---|---|
| --simhub-appid | Override the Steam AppID for the SimHub prefix. |
| --simhub-pfx | Override the path to your Steam compatdata folder. |
| --simhub-exe | The name of the SimHub executable (Default: SimHubWPF.exe). |

**Example setup:**

`   ~/.local/bin/sim-launcher --lookpilot --simhub --acbridge %command%   `

**Example with Fully Kiosk Browser:**

`   ~/.local/bin/sim-launcher --simhub --acbridge --kiosk-ip 192.168.1.100 --kiosk-pw your_password %command%   `

Configuration & Folders
-----------------------

The launcher manages its own environment in ~/.local/share/sim-launcher:


```
   ./sim-launcher
   ├── install/                # Extracted Opentrack portable files
   ├── scripts/                # Generated .bat files for Steam/Proton execution
   ├── simshmbridge/           # Auto-cloned and built simshmbridge repository (when using bridge flags)
   │   └── assets/             # Compiled bridge executables (acshm, acbridge.exe, pcars2shm, pcars2bridge.exe, etc.)
   ├── opentrack-version.txt   # Tracks installed Opentrack version for updates
   └── log_YYYYMMDD.log        # Debug logs (only created if --debug is used, last 5 kept)
```


How it Works
------------

1.  **Detection:** The script parses the Steam %command% to identify the AppID and the Windows executable.

2.  **Auto-Setup:** When bridge flags are used for the first time, simshmbridge is automatically cloned from GitHub and built with the necessary dependencies.

3.  **SimHub Discovery:** SimHub's AppID is automatically located by scanning Steam's `shortcuts.vdf` for non-Steam game entries containing "SimHub". The compatdata path is resolved via Steam library discovery.

4.  **Environment:** It creates a .bat script that Windows/Proton understands to launch the game and the tools together.

5.  **Bridges:** It handles the symlinking and execution of the AC/PC2 Bridge between the Linux environment and the Wine prefix.

6.  **Cleanup:** It uses pkill and os.killpg to ensure no "zombie" processes (like opentrack.exe or acbridge.exe) stay running after you quit the game.
