# Space Invaders (USA) — SNES Recompilation

A native desktop recompilation of **Space Invaders** for the Super Nintendo Entertainment System, based on the USA release.

This repository contains the game-specific recompilation project and build configuration. The shared SNES recompilation framework and desktop UI are included as Git submodules.

## Project status

The native Windows application has been built and tested.

The following have been confirmed working:

- Graphics and rendering
- Audio
- Controller input
- Gameplay

The original SNES game includes a two-player competitive versus mode. This project currently declares one player in its desktop host configuration. Two-player behavior and the original versus mode have not yet been verified in this recompilation.

## ROM requirements

**The original game ROM is not included in this repository or its releases.** You must supply your own compatible ROM.

The project has been configured for the following USA ROM image:

| Property | Expected value |
|---|---|
| Game | Space Invaders (USA) |
| File | `Space Invaders (USA).sfc` |
| Size | 262,144 bytes (256 KiB) |
| Mapping | LoROM |
| Region | USA / NTSC |
| CRC-32 | `caf035f5` |
| MD5 | `925b7c04455633503842e524940a3676` |
| SHA-1 | `125a5a045ab6cb60728c8aded449c7b6a8144035` |
| SHA-256 | `dc5353ddc350816619230f25f8c51bddabf7438e6dfba21662eb1c4794856735` |

The CRC-32 and SHA-256 hashes are used for ROM verification. The other hashes are provided for identification purposes.

A 512-byte SMC copier header, if present, is excluded from verification.

The ROM is used locally to generate the game-specific source code. It should not be committed, uploaded, or bundled with this project. Local ROM configuration and generated files are excluded from version control.

## Building on Windows

### Requirements

The tested build environment uses:

- Windows
- Visual Studio 2026 with the C++ desktop development tools
- CMake
- Git
- Git Bash
- Python (for the regeneration tools)

You will also need a ROM matching the identity described above.

### 1. Clone the repository

Clone the repository with its submodules:

```powershell
git clone --recurse-submodules <repository-url>
cd SpaceInvaders
```

If you already cloned the repository without its submodules, initialize them:

```powershell
git submodule update --init --recursive
```

### 2. Generate the game-specific source

Provide the path to your ROM:

```powershell
bash tools/regen.sh --rom "C:/path/to/Space Invaders (USA).sfc"
```

The regeneration script verifies the ROM against the expected CRC-32 and SHA-256 hashes before generating source code.

You can alternatively set the `SNESRECOMP_ROM` environment variable in PowerShell:

```powershell
$env:SNESRECOMP_ROM = "C:\Games\Space Invaders (USA).sfc"
bash tools/regen.sh
```

If neither `--rom` nor `SNESRECOMP_ROM` is supplied, the script also checks for recognized ROM filenames in the repository root and then the path recorded in the project's `rom.cfg`.

The `--no-verify` option bypasses ROM verification. It is not recommended for normal builds.

Generation produces `src/gen/*.c` and synchronizes `recomp/funcs.h`. Re-run the regeneration script after changing recompilation configuration under `recomp/`.

### 3. Configure the Windows build

The tested CMake generator is Visual Studio 2026:

```powershell
cmake -S . -B build -G "Visual Studio 18 2026" -A x64
```

If you use a different Visual Studio version, substitute its installed CMake generator name.

### 4. Build

Build the Release configuration:

```powershell
cmake --build build --config Release --parallel
```

### 5. Run

The executable is produced at:

```text
build/Release/SpaceInvadersSNESRecomp.exe
```

Launch it with:

```powershell
.\build\Release\SpaceInvadersSNESRecomp.exe
```

## Running the game

When launched without a ROM argument, the application opens the desktop ROM-selection launcher. Select your ROM there.

You can also provide a ROM path directly:

```powershell
.\build\Release\SpaceInvadersSNESRecomp.exe "C:\Games\Space Invaders (USA).sfc"
```

The desktop host supports these command-line options:

| Option | Behavior |
|---|---|
| `--no-launcher` | Skip the launcher and boot directly into the game using the available ROM. |
| `--launcher` | Force the launcher to open, even when a ROM path is supplied. |

The launcher remembers the selected ROM in `rom.cfg` beside the executable.

The launcher's **Skip launcher on boot** setting allows subsequent launches to go directly into the game using the cached ROM. You can force the launcher to appear again with `--launcher`.

The desktop host verifies the selected ROM against the expected game identity before running it.

## Repository layout

| Path | Description |
|---|---|
| `src/` | Game-specific host and project source |
| `src/gen/` | Generated C source; regenerated locally |
| `recomp/` | Game-specific recompilation configuration |
| `snesrecomp/` | Shared SNES recompilation framework (Git submodule) |
| `recomp-ui/` | Desktop UI components (Git submodule) |
| `tools/` | Regeneration and development scripts |
| `rom_identity.txt` | Expected ROM identity and technical properties |
| `LICENSE` | License for this project's own source |

Generated source, local ROM configuration, build output, save data, and user-specific settings are not intended to be committed.

## Licensing and third-party notices

This project's own source is distributed under the **PolyForm Noncommercial License 1.0.0**. See [`LICENSE`](LICENSE) for the complete terms.

The shared framework and desktop UI are included as Git submodules and have their own licensing terms. Review the license files in those submodules before using or redistributing the project.

The framework's third-party attribution document is available at [`snesrecomp/THIRD_PARTY_ATTRIBUTION.md`](snesrecomp/THIRD_PARTY_ATTRIBUTION.md) after initializing the submodule. Retain applicable third-party notices when distributing a package containing the runner.

The project license does not grant rights to the original Space Invaders game, its ROM, or other third-party intellectual property. No game ROM is provided here. Users are responsible for obtaining and using game data in accordance with applicable law and the rights of the relevant owners.
