Linux Sim Launcher
==================

A Python-based utility to synchronize the launch and cleanup of Windows-based simulation tools (**Opentrack, SimHub, LookPilot**) alongside Steam games. It ensures these tools run in the correct Proton prefix and provides an **AC SHM Bridge** for shared memory support on Linux.

Key Features
------------

*   **Auto-updating Opentrack:** Automatically fetches and extracts the latest portable Windows version.
    
*   **SimHub Integration:** Launches SimHub into its own specific Steam AppID prefix using protontricks.
    
*   **AC Bridge:** Supports Assetto Corsa shared memory bridging (Native + Proton components).
    
*   **LookPilot Support:** Toggle-able launch for LookPilot (AppID 3326890).
    
*   **Automatic Cleanup:** When the main game closes, the script automatically kills all associated .exe processes and native bridges.
    

Dependencies
------------

*   **p7zip** (installed as 7z or 7za) - Required for extracting Opentrack.
    
*   **python3** - The core runner.
    
*   **protontricks** - Required for launching SimHub and the Proton Bridge.
    
*   **wget** - For installation.
    

Installation
------------

Open a terminal and choose one of the following methods:
 
```
   # Recommended: Local Install  mkdir -p ~/.local/bin && wget https://raw.githubusercontent.com/giantorth/linux-sim-launcher/master/sim-launcher -O ~/.local/bin/sim-launcher && chmod +x ~/.local/bin/sim-launcher  
   # Alternative: System-wide (Requires sudo)  sudo wget https://raw.githubusercontent.com/giantorth/linux-sim-launcher/master/sim-launcher -O /usr/local/bin/sim-launcher && sudo chmod +x /usr/local/bin/sim-launcher   
```

Usage in Steam
--------------

1.  Right-click your game in Steam -> **Properties**.
    
2.  In the **Launch Options** field, enter the command below.
    

### Basic Usage

`  ~/.local/bin/sim-launcher %command%   `

### Advanced Usage (Flags)

You can append flags to the launcher to enable specific tools:

| Flag | Description |
|---|---|
| --opentrack | Download (if needed) and launch Opentrack. |
| --simhubLaunch | SimHub (enabled by default). |
| --acbridge | Launch the Assetto Corsa Shared Memory Bridge. |
| --lookpilot | Launch LookPilot via Steam. | 
| --debug | Enable verbose logging to file in ~/.local/share/sim-launcher. |

**Example for a full Sim Racing setup:**

`   ~/.local/bin/sim-launcher --opentrack --acbridge %command%   `

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

### SimHub Configuration

If your SimHub is installed in a non-standard Steam AppID or path, you can use these flags:

*   \--simhub-appid: Set the Steam AppID for the SimHub prefix (Default: 2825720939).
    
*   \--simhub-pfx: Path to your Steam compatdata folder.
    
*   \--simhub-exe: The name of the SimHub executable (Default: SimHubWPF.exe).
    

How it Works
------------

1.  **Detection:** The script parses the Steam %command% to identify the AppID and the Windows executable.
    
2.  **Environment:** It creates a .bat script that Windows/Proton understands to launch the game and the tools together.
    
3.  **Bridges:** It handles the symlinking and execution of the AC Bridge between the Linux environment and the Wine prefix.
    
4.  **Cleanup:** It uses pkill and os.killpg to ensure no "zombie" processes (like opentrack.exe or acbridge.exe) stay running after you quit the game.
