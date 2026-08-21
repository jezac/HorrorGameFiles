# HorrorGameFiles
C++ source files from a solo horror game project built in Unreal Engine 5. Covers scripted events, light triggers, and gameplay systems layered on top of a Blueprint foundation. Full project &amp; gameplay footage

## About this repo

This repository contains the C++ source files from a solo horror game project built in Unreal Engine 5. It is **not the full project** - no assets, levels, or Blueprint graphs are included, only the C++ layer.

The project follows a clear split in responsibilities: **C++ handles all gameplay systems and logic** (event scripting, AI-driven danger escalation, ambient audio sequencing, environmental triggers), while **Blueprint handles content-side work** (UI, animations, material instances, input setup). This isn't a limitation - it's a deliberate separation of concerns between systems programming and content implementation.

## Project structure

/Source  
&nbsp;&nbsp;/Player → character, controller, camera (3C)  
&nbsp;&nbsp;/ScriptedEvents → core gameplay systems: triggers, audio, lighting  
&nbsp;&nbsp;/Interfaces → decoupled communication between class types  
&nbsp;&nbsp;/UI → HUD and widget base classes  
&nbsp;&nbsp;/Core → game mode  

## Highlighted systems

### `ScriptedEvents/CPP_Boat`
Scripted sequence actor: speed interpolation between acceleration/deceleration states, a deferred timer triggered by a widget animation event, and Perlin noise driving a secondary visual effect. Demonstrates state-driven actor behavior synced with UI-side animation events.

### `ScriptedEvents/CPP_Sound_swim`
Ambient event actor that approaches a target from a randomized direction, adjusts pitch/volume on proximity detection, and chains into a second actor's function via a dynamic delegate once its sequence completes. Shows event-driven sequencing across decoupled actors.

## Interfaces - decoupled communication

The `/Interfaces` folder contains six `UInterface` classes (`Widget_to_GM`, `HUD_To_Controller`, `Actor_to_Actor`, etc.) used to let Widgets, HUD, Controller, GameMode, and Actor classes communicate without direct casts or hard dependencies between them. Each concrete class implements only the interfaces it needs, keeping systems loosely coupled and Blueprint-callable via `BlueprintNativeEvent`.

## Blueprint / C++ integration

The screenshot below shows the `WBP_Loading_Screen` Event Graph, which orchestrates the transition between levels:

1. Streams the game level in asynchronously (`LoadStreamLevelBySoftObjectPtr`)
2. Unloads the menu level once loading completes
3. Calls `ChangePawnClass` on the GameMode through the `Widget_to_GM` interface
4. Calls `SwitchInput` on the PlayerController through the `Widget_to_Controller` interface
5. Plays a fade-out animation, then collapses the widget

<img width="2014" height="838" alt="ScreenBP_01" src="https://github.com/user-attachments/assets/0cd3207a-f4f4-4b37-a366-ed1df19778e3" />

This graph is the Blueprint-side driver for the asynchronous level streaming system referenced in the C++ source, and ties together two of the interfaces listed above in a single sequence.
