# DrMario64Recompiled


## Overview

Dr. Mario 64 Recomp is a modernized recompilation build focused on improved rendering,
smoother motion, and quality-of-life enhancements while remaining faithful to the original game.

## Highlights (v1.0.0)

- Custom icon set (red and blue capsule)
- Updated main menu with tuned default Recomp64 settings
- Pills are now rendered on the GPU instead of the CPU
- Interpolation support for smooth 60+ FPS gameplay
- 4-player controller support
- CRT effects support

## Requirements

- Dr. Mario 64 (US) ROM  
  A clean, vanilla US ROM is required at runtime.

## Known Issues

- Very lightly tested overall

## Dependencies

- git
- C (C17) and C++ (C++20) compilers  
  - Clang 15 or newer is recommended
- SDL2  
  - Must be built from source on Linux  
  - Example:

    ```bash
    wget https://www.libsdl.org/release/SDL2-2.26.1.tar.gz
    tar -xzf SDL2-2.26.1.tar.gz
    cd SDL2-2.26.1
    ./configure
    make -j $(nproc)
    sudo make install
    ```

- libgtk-3-dev

## Build

For Windows builds, Visual Studio is required. Refer to the Zelda recomp repository for Windows setup guidance.

See [BUILDING.md](BUILDING.md) for instructions on building for Linux.
