# macOS Build Notes

This document summarizes the current macOS Apple Silicon (arm64) build approach and related YM2149 notes:

- Adjusted SDL build handling to add macOS Apple Silicon (arm64) support.
- The bundled SDL2 version in-tree did not build cleanly for this target, so system SDL2 from Homebrew is used for the macOS build path.
- Uses system SDL2 from Homebrew (`brew install sdl2`) instead of vendoring SDL updates in-tree.
- Includes Atari 7800 YM2149 cart-audio support updates (detailed below).
- Keeps macOS build behavior configurable:
  - `USE_LIBSDL=1` selects system SDL.
  - `USE_LIBSDL_STATIC=1` selects static SDL (`libSDL2.a`).
  - Without `USE_LIBSDL_STATIC=1`, system SDL links dynamically (`libSDL2.dylib`).
- Scope is macOS build flow only (`makefile-osx` + macOS SDL logic); Windows/Linux build paths are not modified.

## Prerequisites

```bash
brew install sdl2
```

## Build Commands (Apple Silicon / arm64)

Clean:

```bash
make -f makefile-osx clean PYTHON_EXECUTABLE=python3
```

Dynamic SDL build:

```bash
make -f makefile-osx macosx_arm64 USE_LIBSDL=1 PYTHON_EXECUTABLE=python3 CC=clang CXX=clang++
```

Static SDL build:

```bash
make -f makefile-osx macosx_arm64 USE_LIBSDL=1 USE_LIBSDL_STATIC=1 PYTHON_EXECUTABLE=python3 CC=clang CXX=clang++
```

## Quick Validation

```bash
file mame
./mame -help
```

For dynamic builds, `otool -L mame` should show an SDL2 dylib.
For static builds, `otool -L mame` should not list SDL2.

## YM2149 Work In Progress

- YM2149 currently works in emulation; this work also supports the effort to add YM2149 support to real cartridge hardware.
- Added initial YM2149 cart support using A78 header v4 `audio_hi` bit 6 (`YM2149 @ $4000/$4001`).
- Extended cart type detection/selection to YM2149 variants (including POKEY/SuperGame combinations).
- Added YM2149-capable cart device types and mapped YM2149 register writes via `$4000/$4001`.
- Updated header logging to report YM2149 presence when v4 header fields are available.

Primary files involved:

- `src/devices/bus/a7800/a78_slot.cpp`
- `src/devices/bus/a7800/rom.cpp`
- `src/devices/bus/a7800/rom.h`
- `src/devices/bus/a7800/a78_carts.h`
- `src/devices/bus/a7800/a78_slot.h`
- `src/mame/drivers/a7800.cpp`
