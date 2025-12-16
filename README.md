# Godot engine build containers

This repository contains the Dockerfiles for the official Godot engine builds.
These containers should help you build Godot for all platforms supported on
any machine that can run Docker containers.

The in-container build scripts are in a separate repository:
https://github.com/godotengine/godot-build-scripts


## Introduction

These scripts build a number of containers which are then used to build final
Godot tools, templates and server packages for several platforms.

Once these containers are built, they can be used to compile different Godot
versions without the need of recreating them.

The `upload.sh` file is meant to be used by Godot Release Team and is not
documented here.


## Requirements

These containers have been tested under currently supported Fedora releases
(other distros may work too).

The tool used to build and manage the containers is `podman` (install it with
`dnf -y podman`).

We currently use `podman` as root to build and use these containers. Documenting
a workflow to configure the host OS to be able to do all this without root would
be welcome (but back when we tried we ran into performance issues).


## Usage

The `build.sh` script included is used to build the containers themselves.

The two arguments can take any value and are meant to convey what Godot branch
you are building for (e.g. `4.6`) and what Linux distribution the `Dockerfile.base`
is based on (e.g. `f43` for Fedora 43).

Run the command using:

    ./build.sh 4.6 f43

The above will generate images using the tag '4.6-f43'.
You can then specify it in the `build.sh` of
[godot-build-scripts](https://github.com/godotengine/godot-build-scripts).


### Selecting which images to build

If you don't need to build all versions or you want to try with a single target OS first,
you can comment out the corresponding lines from the script:

    podman_build linux
    podman_build windows
    podman_build web
    podman_build android
    ...


## Image sizes

These are the expected container image sizes, so you can plan your disk usage in advance:

    REPOSITORY                         TAG                SIZE
    localhost/godot-fedora             4.6-f43            978 MB
    localhost/godot-linux              4.6-f43            2.77 GB
    localhost/godot-windows            4.6-f43            2.65 GB
    localhost/godot-web                4.6-f43            2.74 GB
    localhost/godot-android            4.6-f43            4.22 GB
    localhost/godot-xcode              4.6-f43            1.56 GB
    localhost/godot-osx                4.6-f43            14.6 GB
    localhost/godot-appleembedded      4.6-f43            16.0 GB

In addition to this, generating containers will also require some host disk space
(up to 10 GB) for the dependencies (Xcode).


## Toolchains

These are the toolchains currently in use for Godot 4.3 and later:

- Base image: Fedora 43
- SCons: 4.10.1
- Linux: GCC 13.2.0 built against glibc 2.28, binutils 2.40, from our own [Linux SDK](https://github.com/godotengine/buildroot)
- Windows:
  * x86_64/x86_32: MinGW 13.0.0, GCC 15.2.1, binutils 2.45
  * arm64: llvm-mingw 20251118, LLVM 21.1.6
- Web: Emscripten 4.0.20
- Android: Android NDK 28.1.13356709, build-tools 35.0.1, platform android-35, CMake 3.31.6, JDK 21
- Apple: Xcode 26.1.1 with Apple Clang (LLVM 19.1.5), cctools 1030.6.3, ld64 956.6
  * SDKs: MacOSX, iPhoneOS, iPhoneSimulator, AppleTVOS, AppleTVSimulator, XROS, XRSimulator
