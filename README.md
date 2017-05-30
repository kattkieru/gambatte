GBDK3

Gambatte Gameboy Color Emulator
===============================

This is a direct clone from the main repo:
https://github.com/sinamas/gambatte

The CMakeLists.txt file included will bootstrap an SDL build. I'm not doing anything special; I just want to make sure that I have a locked version for the Toolchain that I can include using a git submodule and a cmake subdirectory.

This repo expects to find my libsdl 1.2.15 repo living next to it:
https://github.com/kattkieru/sdl

It will generate the appropriate static libs automatically as part of the build process.

~ k


