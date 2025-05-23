# Introduction -- Restoring and Modifying Classic *Doom*
## *!!! THIS WILL ONLY RUN ON LINUX DUE TO THE ORIGINAL SOURCE CODE !!!*
## Total Time Spent: 17 hours

### How To Try This Project -- Quickstart Guide

#### This is the correct `README.md`. The others are from the legacy code and have been left in because I think they're interesting to read.

- This project *must* be run on a Linux machine due to limitiations with the original source code
- Change directories into the `linuxdoom-1.10` directory
- Run the following commands in shell instance `1`:
    - `sudo apt install libx11-dev`
    - `sudo apt install libxext-dev`
    - `sudo apt install libnsl-dev`
    - `sudo apt install xserver-xephyr`
    - `sudo Xephyr :2 -ac -screen 640x480x8`
- Run the following commands in shell instance `2`:
    - `DISPLAY=:2`
    - `cd DOOMtesting/DOOMtest/`
    - `./linux/linuxxdoom -2`

#### Video Demonstration: https://youtu.be/4aUcIFAZLzQ

***

## Phases

### Project setup

#### The first thing I did was to learn about and fix a multitude of build errors, which are explained below:

After immediately forking the Doom source code (https://github.com/id-Software/DOOM), there were quite a few build errors to fix. Each is noted below with a very brief explanation of the problem fixed:

- `i_video.c line 49`: changed `#include <errnos.h>` to `#include <errno.h>`
    - Fixed the incorrect header file inclusion

- `i_video.c line 820`: added line `XInstallColormap( X_display, X_cmap );`
    - Ensured the X server colormap is installed, needed for color rendering in X11 environment.

- `m_misc.c line 229`: changed `int		defaultvalue;` to `long long		defaultvalue;`
    - Increased the size of the `defaultvalue` variable

- `m_misc.c line 257`: changed `(int) "sndserver"` to `(long long int) "sndserver"`
    - Updated the cast to the `long long` type, which was necessary for use with the newer compiler

- `m_misc.c line 264`: changed `(int)"/dev/ttyS0"` to `(long long int)"/dev/ttyS0"`
    - Updated the cast to the `long long` type, which was necessary for use with the newer compiler

- `m_misc.c line 265`: changed `(int)"microsoft"` to `(long long int)"microsoft"`
    - Updated the cast to the `long long` type, which was necessary for use with the newer compiler

- `m_misc.c lines 288-297`: changed each instance of `(int) HUSTR_CHATMACROX` to `(long long int) HUSTR_CHATMACROX`
    - Updated the cast to the `long long` type, which was necessary for use with the newer compiler

- `i_sound.c line 166`: removed line `extern int errno;`
    - Did not need this line, which caused a conflict

- `i_sound.c line 43`: added line `#include <errno.h>`
    - Needed for error handling

- `r_data.c line 30`: added line `#include <stdint.h>`
    - Included header for integer types

- `r_data.c line 92`: changed `void       **columndirectory;` to `int         columndirectory;`
    - Corrected the type of `columndirectory`

- `r_data.c line 484`: changed `numtextures*4` to `numtextures*sizeof(*textures)`
    - Changed memory allocation size to match the size of of the `textures` elements

- `r_data.c line 485`: changed `numtextures*4` to `numtextures*sizeof(*texturecolumnlump)`
    - Changed memory allocation size to match the size of of the `texturecolumnlump` elements

- `r_data.c line 486`: changed `numtextures*4` to `numtextures*sizeof(*texturecolumnofs)`
    - Changed memory allocation size to match the size of of the `texturecolumnofs` elements

- `r_data.c line 487`: changed `numtextures*4` to `numtextures*sizeof(*texturecomposite)`
    - Changed memory allocation size to match the size of of the `texturecomposite` elements

- `r_data.c line 644`: changed `(int)colormaps` to `(intptr_t)colormaps`
    - Changed cast to use `intptr_t`, a pointer-sized integer type, which was needed to run on modern compiler

- `r_draw.c line 30`: added line `#include <stdint.h>`
    - Included header for integer types

- `r_draw.c line 464`: changed `(int)translationtables` to `(intptr_t)translationtables`
    - Changed cast to use `intptr_t`, a pointer-sized integer type, which was needed to run on modern compiler

- `p_setup.c line 536`: changed `total*4` to `total*sizeof(*linebuffer)`
    - Changed memory allocation size to match the size of of the `linebuffer` elements

The following dependencies must be installed. Each is responsible for handling files related to the X11 environment:

- `libx11-dev`
    - Installed with `sudo apt install libx11-dev`

- `libxext-dev`
    - Installed with `sudo apt install libxext-dev`

- `libnsl-dev`
    - Installed with `sudo apt install libnsl-dev`

Additionally, *Doom* requires an 8-bit display to run. I used Xephyr (a display software implementing the X11 display server protocol), installed using `sudo apt install xserver-xephyr`. The display can be started with `Xephyr :2 -ac -screen 640x480x8` (will be black until running *Doom*). In another terminal window, run the executable `linuxxdoom` with the `DISPLAY` environment variable set to `2`, i.e. `DISPLAY=:2`. (Further reading: https://en.wikipedia.org/wiki/Xephyr)

***

### First Modification: Hyperspeed! (player)
#### The first modification I implemented was higher player speed.

This modification was quite simple, and only took changing a few lines of code. I didn't even need any knowledge of C or understanding of the game engine to implement this. I simply parameterized the `P_MovePlayer` function and declared a global variable `modded_speed` at the beginning of the `p_user.c` file. (It was initially 2048, which I doubled to 4096)

***

### Second Modification: *SUPER* Hyperspeed! (enemy)
#### The second modification I implemented was far higher enemy speed.

This modification was also quite simple, and is very comical to observe. The enemy speed was simply stored in an array `mobjinfo` in `info.c`. Then, due to good documentation, I was able to easily locate the speed value of the Former Humans, Former Human Sergeants, and Imps, and manually multiply each speed value by `5`. This results in very fast enemies that are quite hard to gun down in the game.

***

### Third modification: Hyperspeed! (weapons)
#### The third modification I implemented was higher fire rate for three weapons.

This modification was a bit more challenging to implement, but was again as simple as modifying constant values in the `info.c` file; in the `states` array (line 135), each weapon's fire rate is defined with a tic delay before that weapon can be fired. I found the data for the in-game punch attack, the pistol, and the shotgun, which are the first three attainable weapons, and reduced each to the minimum value of `1`. The result is Mike Tyson-level punching speed and inhuman firing speed of the other two weapons. (Unfortunately, the very fast player speed makes this impractical.)

***

### Fourth modification: Aurora Borealis (background)
#### The fourth modification I implemented was a beautiful background to gaze upon.

I decided that my monster-slaying wasn't quite aesthetic enough. I've always loved the Northern Lights, so I decided to make the sky have the texture. Unfortunately, this was *far* more complicated than expected. I had to learn about IWADs (internal WADs that contain all the game data), PWADs (path WADs which contain patches or "overwrites" to the game data), how to format an image appropriately so that the Doom engine will read it properly, and a multitude of other niche bits of Doom knowledge. (To view this effect, gaze out the window of the first window. It's on the right of the very first level as soon as you walk out of the elevator.)

***

### Fifth modification: Custom DOOM Text
#### The fifth modification I implemented was custom background text on the title screen.

For this modification, I used my newly-acquired WAD editing skills to spice up the background text! On the title screen, instead of "DOOM", it now reads "CS2810 Final." For this modification, I once again had to dig through the WADs, locate the texture that represents the "DOOM" text on the title screen, make my *own* texture that reads "CS2810", convert it into a Doom Palette compatible texture using SLADE, and then replace the original texture. I'm pretty happy with how it turned out, but the Doom palette (8-bit color) is pretty restrictive.

***

### Sixth modification: Free weapons
#### The sixth modification I implemented was adding more weapons to the user's inventory at game start.

I wanted to be able to try out a few more weapons at the start of the game, so I decided to add those to the player's inventory when they enter the game. This wasn't difficult: I just had to learn a bit of C syntax, and essentially use the pattern that I observed in the other lines, i.e. `p->weaponowned[wp_fist] = true;` and `p->weaponowned[wp_pistol] = true;` to add the shotgun, chaingun, chainsaw, and "BFG" and plasma gun (which don't work, since they're acquired in a later level; only their ammo appears) to allow the player access to some items early on in the game.

***

### Seventh modification: Familiar face
#### The seventh modification I implemented was replacing Doomguy's asset at the bottom of the screen with Dr. Harper's face

I couldn't stand Doomguy's face grimacing at me from the bottom of the screen's HUD, so instead, I decided that I wanted the cheerful, smiling face of our very own Dr. Harper! I followed similar steps as I did in earlier asset replacements; downloading Professor Harper's image, preparing the original image by cropping and resizing it and removing its background, converting it into a Doom Graphic, mapping its colors to the Doom palette, and replacing Doomguy's asset in the `doom1.wad` file. Additionally, I needed to remove the `ST_updateFaceWidget` function in `st_stuff.c` so that the face wouldn't update based on in-game damage taken, weapons picked up, etc. The result is amusing, if a bit creepy!

***

### Eighth modification: Crosshair
#### The eighth modification I implemented was a crosshair, which is notably missing from Classic *Doom*. 

I decided that I wanted a crosshair to help see where I was aiming when playing *Doom*, so I've added a crosshair to the center of the screen. To do this, I needed to learn about *Doom*'s rendering engine. I read `st_stuff.c`, where a lot of status bar code lives, and `v_video.c`, where code to render images on the screen lives. I wrote the routines `ST_DrawCrosshair` and `V_DrawPixel` which were simple enough. Essentially, the crosshair is an overlay, rendered after the game world and before the HUD. Then, I needed to call the routine in the `d_main.c` file.  The V_DrawPixel routine is particularly elegant (in my humble opinion). 

***

## Summary of Changes from Original Source Code

This project involved restoring and modifying original *Doom* source code. The changes made are summarized below:

- **Build Fixes**: Addressed numerous build errors to make the code compatible with modern compilers and systems, including header file corrections, type adjustments, memory allocation fixes, and removal of obsolete and conflicting code

- **Dependencies and Environment Setup**: Installed necessary libraries (`libx11-dev`, `libxext-dev`, `libnsl-dev`) and configured an 8-bit display environment using Xephyr to run the game.

- **Gameplay Modifications**:
    - Increased player speed.
    - Increased enemy speed.
    - Reduced weapon fire rate delays for faster firing.

- **Visual Enhancements**:
    - Replaced the mountainous sky texture with a beautiful Aurora Borealis background.
    - Customized the title screen "DOOM" text to instead display "CS2810 Final."
    - Replaced Doomguy's HUD face with an image of Professor Harper.

- **Gameplay Additions**:
    - Added more weapons to the player's inventory at the start of the game.
    - Implemented a (noticeably missing) crosshair at the center of the screen.

These changes added fun twists to the game while keeping its core, making a fun and original experience for this final project.

***

## Conclusion

In conclusion, I had the opportunity to learn many things during this project, applying principles from CS2810 along the way:

- **Mid-to-low-level programming**
    - The source code for *Doom* was unlike any code I've worked with before:
        - I've never dabbled in C code, so it was a challenge to familiarize myself with the syntax.
        - I've never worked on such a large-scale project before, so it was interesting to see how everything worked together.
        - Almost all the code I've written before this project simply used the terminal to render, so working with a rendering engine was new to me.

- **Understanding compilers**
    - The legacy code directly forked from id Software's work was meant for old compilers.
    - As detailed above, I needed to comb through the numerous build errors, correcting them to work on a modern compiler.
    - One of the toughest pills to swallow was learning that due to some outdated code, the sound system wouldn't work on modern Linux machines' compilers, and it was far outside my abilities to restore it.
    - I needed to learn about how *Doom*'s `WAD` system worked to be able to implement asset changes like changing Doomguy's face to Professor Harper's.

- **Testing and debugging low-level languages**
    - I had the opportunity to use my skill in testing and debugging low-level languages for every feature that I implemented.

- **Building on existing work/source code**
    - This was a new experience for me outside of CS2810 coursework.
    - I had the opportunity to learn a lot about using others' code (in this case the *Doom* engine), reading others' code, and adding my own twists (gameplay mechanics, graphics, etc.)

**Overall, this project provided me with a unique opportunity to apply the concepts that I've learned in CS2810. This ended up being a very enjoyable project, and I learned a *lot*. My personal favorite addition is the Aurora Borealis sky, which also required me to learn a lot about *Doom*'s graphics and rendering engine. I've learned so much from this project, and I think I may even take another crack at classic game modding in the future, perhaps with some more ambitious goals such as changing core game mechanics. I'm grateful for everything I've learned from CS2810, the opportunity to apply my knowledge in this project, and I'm so grateful to be able to take the class from you, Professor!**