# macOS Build Notes

This document summarizes the current macOS Apple Silicon (arm64) build approach:

- Adjusted SDL build handling to add macOS Apple Silicon (arm64) support.
- The bundled SDL2 version in-tree did not build cleanly for this target, so system SDL2 from Homebrew is used for the macOS build path.
- Uses system SDL2 from Homebrew (`brew install sdl2`) instead of vendoring SDL updates in-tree.
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
make -f makefile-osx clean
```

## Build Commands (Apple Silicon / arm64)

Dynamic SDL build:

```bash
make -f makefile-osx macosx_arm64 USE_LIBSDL=1 CC=clang CXX=clang++
```

Static SDL build:

```bash
make -f makefile-osx macosx_arm64 USE_LIBSDL=1 USE_LIBSDL_STATIC=1 CC=clang CXX=clang++
```
## Quick Validation

```bash
file mame
./mame -help
```

For dynamic builds, `otool -L mame` should show an SDL2 dylib.
For static builds, `otool -L mame` should not list SDL2.
