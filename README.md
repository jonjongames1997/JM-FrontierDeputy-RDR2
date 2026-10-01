# JM-FrontierDeputy v0.1.3

JM-FrontierDeputy adds a configurable, procedural sheriff-deputy gameplay loop to **Red Dead Redemption 2 Story Mode**.

This first playable foundation includes duty stations, dispatch offers, three callouts, multiple suspect outcomes, vanilla-compatible arrests, prisoner transport, progression, pay, persistence, hot-reloadable configuration, and an extensible callout API.

## Current features

- Begin and end shifts at four sheriff offices:
  - Valentine
  - Rhodes
  - Saint Denis
  - Blackwater
- Automatic weighted dispatch with configurable delays.
- Manual callout requests.
- Accept, decline, timeout, abort, failure and cleanup handling.
- Three procedural callouts:
  - Saloon Disturbance
  - Roadside Robbery
  - Wanted Outlaw
- Random suspect behavior:
  - Surrender
  - Armed or unarmed resistance
  - Flight
- Detects vanilla lasso/hogtie arrests.
- Contextual cuff-and-escort flow for surrendering suspects.
- Prisoner booking at the nearest configured sheriff office.
- Hogtied suspects can be transported through RDR2's normal carry and horse systems.
- Persistent JSON deputy profile:
  - Calls accepted, completed and failed
  - Arrests
  - Lethal outcomes
  - Experience and rank
- Four ranks: Deputy, Senior Deputy, Chief Deputy and Marshal.
- Optional callout pay.
- Configurable station and scene locations in `Locations.json`.
- INI and location hot reload with `F11` while no dispatch activity is active.
- Defensive cleanup and diagnostic logging.

## Requirements

- Red Dead Redemption 2 for PC, Story Mode
- A ScriptHookRDR2 build compatible with your installed game version
- A ScriptHookRDR2DotNet V2 runtime explicitly compatible with that ScriptHook build
- .NET Framework 4.8
- Microsoft Visual C++ 2015–2022 Redistributable (x64)

The ScriptHook runtime is not bundled. `ScriptHookRDRDotNet.asi`,
`ScriptHookRDRNetAPI.dll`, and `ScriptHookRDRDotNet.ini` must come from the
same ScriptHookRDR2DotNet package. Mixing the original Halen84 V2 files with a
different or patched API DLL can prevent every C# script from loading.

If you use the newer ScriptHookRDR2 V2 replacement, install a
ScriptHookRDR2DotNet V2 compatibility build made specifically for it. The
unmodified runtime was designed for Alexander Blade's C++ ScriptHookRDR2.

## Installation

Copy the contents of the release archive into the folder containing `RDR2.exe`.

The result should look like:

```text
Red Dead Redemption 2/
├── ScriptHookRDR2.dll
├── dinput8.dll or another compatible ASI loader
├── ScriptHookRDRDotNet.asi
├── ScriptHookRDRNetAPI.dll
├── ScriptHookRDRDotNet.ini
└── scripts/
    ├── JM-FrontierDeputy.dll
    └── JM-FrontierDeputy/
        ├── JM-FrontierDeputy.ini
        └── Locations.json
```

Launch Story Mode, visit a configured sheriff office and use `Context / E` or `F10` to begin a shift.

## Updating from v0.1.0–v0.1.2

Copy the v0.1.3 archive into the folder containing `RDR2.exe` and allow it to
replace `scripts/JM-FrontierDeputy.dll`. Your INI, locations, and deputy profile
remain compatible.

v0.1.0 could resolve its data directory as
`scripts/scripts/JM-FrontierDeputy` under ScriptHookRDR2DotNet's child domain.
After confirming v0.1.3 works, any files in that accidental directory may be
moved to `scripts/JM-FrontierDeputy` and the extra `scripts/scripts` directory
can be removed.

## Default controls

| Control | Action |
|---|---|
| `Context / E` | Interact, arrest surrendering suspect, book prisoner |
| `F10` | Begin/end shift while at a sheriff office |
| `F7` | Request a callout |
| `Y` | Accept a dispatch offer |
| `N` | Decline a dispatch offer |
| `End` | Abort the active callout |
| `F6` | Show deputy status and statistics |
| `F11` | Reload INI and locations |

Keyboard hotkeys can be changed in `JM-FrontierDeputy.ini`.
The interaction key is configured with `Interact = E` under `[Keys]`. v0.1.2
and later check both RDR2's native Context control and ScriptHook's keyboard event so the
interaction remains responsive across compatible runtime variants.

## Arrest and transport flow

1. Approach the incident and assess the suspect.
2. If the suspect surrenders, approach and press `Context / E` to cuff them.
3. If the suspect fights or flees, use RDR2's normal lasso and hogtie mechanics for a live arrest.
4. Follow the booking marker to the nearest sheriff office.
5. A cuffed suspect will attempt to follow on foot. A hogtied suspect can be carried or placed on your horse.
6. Bring both the player and prisoner into the booking area and press `Context / E`.

If you are standing at the booking point but the prisoner is too far away, the
mod displays the prisoner's remaining distance. Bring a carried, escorted, or
horse-mounted prisoner within the allowed booking radius before pressing the
configured interaction key.

## Configuration and persistence

- `JM-FrontierDeputy.ini` controls keys, dispatch timing, gameplay distances, pay, callout enablement and callout weights.
- `Locations.json` controls duty stations, jail turn-in points and callout scenes.
- `data/DeputyProfile.json` is created automatically.
- `JM-FrontierDeputy.log` is created automatically. Enable detailed entries with `EnableDebugLogging = true`.

Keep JSON syntax valid when editing `Locations.json`. Scene `type` values must be exactly `SaloonDisturbance`, `RoadsideRobbery` or `WantedOutlaw` for this release.

## Important limitations of v0.1.x

- This is the first playable foundation and should be treated as a beta.
- The included coordinates are initial defaults and should be verified in-game across story chapters.
- Custom law, population and world-persistence mods may alter NPC behavior.
- There is no custom deputy uniform, badge, partner, evidence system or prisoner wagon yet.
- Only one active suspect is managed by each v0.1.x callout.
- The mod does not support Red Dead Online.

## Source layout

- `JM-FrontierDeputy.sln` — Visual Studio 2022 solution with Debug/x64 and Release/x64 configurations
- `src/JM.FrontierDeputy` — C# project targeting .NET Framework 4.8/x64
- `config` — default user configuration and locations
- `docs` — build guide, extension guide and runtime test plan
- `release` — assembled installation layout after a successful build

See [BUILD.md](docs/BUILD.md) and [VISUAL_STUDIO_2022.md](docs/VISUAL_STUDIO_2022.md)
to compile, and [CALLOUT_AUTHORING.md](docs/CALLOUT_AUTHORING.md) to extend the system.

## Support checklist

After entering Story Mode, wait up to ten seconds. A successful startup creates:

```text
scripts/JM-FrontierDeputy/JM-FrontierDeputy.log
```

Its first entries should contain `bootstrap loaded` followed by `initialized`.
Also inspect `ScriptHookRDRDotNet.log` in the game folder:

- No ScriptHookRDRDotNet log: the ASI/.NET runtime did not start.
- Runtime log exists but does not find `JM-FrontierDeputy.dll`: the archive is
  installed in the wrong directory.
- Runtime log reports `Unable to resolve API version 2.2`: the ASI and API DLL
  do not match.
- Mod log contains `bootstrap loaded` but not `initialized`: use the exception
  directly below it when requesting support.
- The booking prompt appears but does not complete: confirm the INI contains
  `Interact = E`, then look for `Booking ready` and `Booking interaction
  accepted` in the mod log.

When reporting a problem, include:

- Game build
- ScriptHookRDR2 version
- ScriptHookRDR2DotNet V2 version/fork
- `JM-FrontierDeputy.log`
- Active callout and location
- Other law or NPC-overhaul mods installed

Copyright © 2026 JM Modifications. All rights reserved.
