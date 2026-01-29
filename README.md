Linux Sim Launcher
==================

A Python-based utility to synchronize the launch and cleanup of Windows-based simulation tools (**Opentrack, SimHub, LookPilot**) alongside Steam games. It ensures these tools run in the correct Proton prefix and provides an **Assetto Corsa SHM Bridge** for shared memory support on Linux.  This solves the need to run SimHub in a single prefix to simplify licensing and configuration instead of installing it in every game prefix.

You can find me on the [Sim Racing on Linux](https://simracingonlinux.com/) [discord](https://discord.simracingonlinux.com/) if you have any questions.

Key Features
------------

*   **Auto-updating Opentrack:** Automatically fetches and extracts the latest portable Windows version.
    
*   **SimHub Integration:** Launches SimHub into its own specific Steam AppID prefix using protontricks.

*   **Monocoque**: Launch Monocoque with the game.

*   **AC Bridge:** Supports Assetto Corsa shared memory bridging (Native + Proton components).

*   **PC2 Bridge:** Supports Automobilista 2/Project Cars 2 shared memory bridging (Native + Proton components).
    
*   **LookPilot Support:** Toggle-able launch for LookPilot (AppID 3326890).
    
*   **Automatic Cleanup:** When the main game closes, the script automatically kills all associated .exe processes and native bridges.
    

Dependencies
------------

*   **p7zip** (installed as 7z or 7za) - Required for extracting Opentrack.
    
*   **python3** - The core runner.
    
*   **protontricks** - Required for launching SimHub and the Proton Bridge.

*   **[simshmbridge](https://github.com/spacefreak18/simshmbridge)** - Required for AC/ACE/ACR briding to simhub
    
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
* Ensure you have compiled a working copy of [simshmbridge](https://github.com/spacefreak18/simshmbridge), follow directions on that repo to have the required tools.

  * If you don't have it, install protontricks from your distro's package repo.

* Install simhub in it's own Steam prefix:

    1. Add the SimHub installer to Steam as a **non-steam game** 
    2. Edit the properties to set compatability options to a GE-Proton (better dotnet compatability)
    3. Run the SimHub installer and **make sure to uncheck** "Install Microsoft redistributables" and "Install USB display drivers" (they don't work on linux)
    4. Locate the appid for this new prefix (Tell steam to add it as a desktop shortcut and inspect the properties for the ID)
    5. Install dotnet48 in this prefix (Replace ID_OF_SIMHUB with correct value)

    ```bash
    $ WINEPREFIX=~/.steam/steam/steamapps/compatdata/ID_OF_SIMHUB/pfx winetricks -q --force dotnet48
    ```
    6. Edit the properties in Steam and browse to the correct executable (be sure to add quotes if Steam doesnt) example path: `"/home/USERNAME/.steam/steam/steamapps/compatdata/ID_OF_SIMHUB/pfx/drive_c/Program Files (x86)/SimHub/SimHubWPF.exe"`
    7. Ensure SimHub launches correctly from Steam
    8. Edit the script parser arguments to add your unique SimHub appid as the default (if desired)

Potential Issues
----------------
* Some games require additional configuration to support SimHub.  Normally SimHub would configure this automatically for you but is unable to when running in it's own prefix.  Either look up instructions on how to manually configure the game, or follow [these directions](https://gist.github.com/srlemke/617fe318ea26fed4cbd2edaec9209c86) to install a demo copy in a game's prefix so it is able to auto-configure the game for you.
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
| --acbridge | Launch the Assetto Corsa (AC/ACE/ACR) Shared Memory Bridge. |
| --pc2bridge | Launch the Project Cars 2 (Automobilista 2) Shared Memory Bridge. |
| --lookpilot | Launch LookPilot via Steam. | 
| --debug | Enable verbose logging to file in ~/.local/share/sim-launcher. |
| --simhub-appid | Set the Steam AppID for the SimHub prefix (Default: 2825720939). |
| --simhub-pfx | Path to your Steam compatdata folder. |
| --simhub-exe | The name of the SimHub executable (Default: SimHubWPF.exe). |
| --simhub-same-prefix | Launch Simhub from the same prefix as the game, must already be installed |

**Example setup:**

`   ~/.local/bin/sim-launcher --lookpilot --simhub --acbridge %command%   `

Configuration & Folders
-----------------------

The launcher manages its own environment in ~/.local/share/sim-launcher:


```
   ./sim-launcher  
   ├── install/                # Extracted Opentrack portable files  
   ├── scripts/                # Generated .bat files for Steam/Proton execution  
   ├── opentrack-version.txt   # Tracks installed Opentrack version for updates  
   └── log_YYYYMMDD.log        # Debug logs (only created if --debug is used)   
```
    

How it Works
------------

1.  **Detection:** The script parses the Steam %command% to identify the AppID and the Windows executable.
    
2.  **Environment:** It creates a .bat script that Windows/Proton understands to launch the game and the tools together.
    
3.  **Bridges:** It handles the symlinking and execution of the AC/PC2 Bridge between the Linux environment and the Wine prefix.
    
4.  **Cleanup:** It uses pkill and os.killpg to ensure no "zombie" processes (like opentrack.exe or acbridge.exe) stay running after you quit the game.
