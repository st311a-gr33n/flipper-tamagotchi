Tama P1 Emulator for Flipper Zero
=======================================

This is a tama P1 Emulator app for Flipper Zero, based on [TamaLIB](https://github.com/jcrona/tamalib/).

![Alt Text](tama.gif)

How to play
-----------
Create a `tama_p1` folder in your microSD card, and put the ROM as `rom.bin`.
Use a search engine to find the Tamagotchi ROM. There is a file named `tama.b`. 
Rename this to `rom.bin`. 

*Controls in portrait mode are the same as landscape mode, but turned 90 degrees.*
- Left button is A.
- Down or OK is B. 
- Right button is C. 
- Up button takes you to the emulator menu.
- Hold the Back button to save and exit.
- Hold the Down button (B) to lock the screen. While locked, the game keeps
  running but input is ignored and the screen shows an unlock hint. Press the
  Back button three times within 2 seconds to unlock. The screen also locks
  automatically after 60 seconds of no input.

![Alt Text](Screenshot1.png)
![Alt Text](Screenshot2.png)

Building
--------
Move this folder into flippers `applications_user/TAMA-P1`. 


Launching the app, directly from console to flipper: 
```
./fbt launch_app APPSRC=applications_user/TAMA-P1
```

Run the following to compile icons:
```
scripts/assets.py icons applications_user/TAMA-P1/icons applications_user/TAMA-P1/compiled
```

Note: you may also need to add `-Wno-unused-parameter` to `CCFLAGS` in
`site_cons/cc.scons` to suppress unused parameter errors in TamaLIB.

Debugging
---------
Using the serial script from [FlipperScripts](https://github.com/DroomOne/FlipperScripts/blob/main/serial_logger.py) 
it is easy to add direct logging after running the application: 
```
`python .\serial_logger.py`

`./fbt launch_app APPSRC=applications_user\TAMA-P1;  python .\serial_logger.py`
```
Alternatively, follow the directions here: https://flipper.atmanos.com/docs/debugging/viewing/

Implemented
-----------
- Menu options:
  - Switch between portrait and landscape
  - A+C shortcut (mute/change in-game time)
  - Double / quadruple speed

![Alt Text](Screenshot3.png)

To-Do
-----
- Fix bugs: 
  - When not on 1x speed, after mashing buttons in quick succession, buttons stop responding for a few seconds. But the rom still runs.
- Stuff to do when bored:
  - optimization and bug fixing (see above)
  - add to this list
  - portrait menu
  - Add "loading bar" when saving
  - "Advanced" settings
  - saving and loading, multiple save states, with the date and time of of each save.
  - Autosave and changing autosave frequency
  - Save settings to /tama_p1/settings.txt

![Alt Text](Screenshot4.png)

Project memory
--------------
`tama-p1-vault/` tracks architecture, ADRs, progress, and gaps. Open it in
Obsidian/Tolaria; AI agents follow [`AGENTS.md`](tama-p1-vault/AGENTS.md). See
[Home](tama-p1-vault/Home.md).
