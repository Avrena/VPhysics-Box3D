# VPhysics Box3D

A replacement for Source's VPhysics module (IVP/Havok), built on [Box3D](https://github.com/erincatto/box3d), Erin Catto's 3D physics engine. One build works for both Garry's Mod and Source SDK 2013 Multiplayer games.

> [!NOTE]
> This repository is a fork of [Asphaltian/VPhysics-Box3D](https://github.com/Asphaltian/VPhysics-Box3D). Releases, the issue tracker and the [Discord server](https://discord.gg/rbCJdZjewf) are upstream.

## Status

Version 0.2.0 is a full rewrite that follows the original VPhysics behaviour. Box3D itself is still alpha software (v0.1.0), so some physics will feel different from stock. Known differences are tracked in the [upstream issues](https://github.com/Asphaltian/VPhysics-Box3D/issues).

## Compatibility

### Garry's Mod branches

| Branch | Supported |
|:--|:--|
| `public` (default) and `dev` | Yes |
| `x86-64` beta | **No.** The game fails to start with this module on this branch. Facepunch plans to retire it; use `public` or `dev`, which include 64-bit Windows builds. |

Garry's Mod does not ship 64-bit Linux builds on `public` or `dev` yet (November 2026). Until it does, Garry's Mod on Linux uses the 32-bit (`x86`) builds.

### Builds

| Preset | Output | Use for | Tested upstream |
|:--|:--|:--|:--:|
| `windows-x64` | `vphysics.dll` | 64-bit Windows game and dedicated server | ✔️ |
| `windows-x86` | `vphysics.dll` | 32-bit Windows game and dedicated server | ✔️ |
| `linux-x64` | `vphysics.so` | 64-bit Linux game (Source SDK 2013) | |
| `linux-x86` | `vphysics.so` | 32-bit Linux game | |
| `dedicated-linux-x64` | `vphysics_srv.so` | 64-bit Linux dedicated server (Source SDK 2013) | |
| `dedicated-linux-x86` | `vphysics_srv.so` | 32-bit Linux dedicated server | ✔️ |

Windows dedicated servers load the same `vphysics.dll` as the game, so there is no separate Windows dedicated build.

## Installing

1. Download the zip for your platform from the [upstream releases](https://github.com/Asphaltian/VPhysics-Box3D/releases). The `dedicated-linux` zips are for Linux dedicated servers; Windows dedicated servers use the Windows zips.
2. Back up the game's own `vphysics` file, then replace it with the one from the zip:

   | Game | Windows x64 | Windows x86 | Linux x64 | Linux x86 |
   |:--|:--|:--|:--|:--|
   | Garry's Mod | `bin/win64/vphysics.dll` | `bin/vphysics.dll` | not available yet | `bin/vphysics.so` |
   | Garry's Mod Dedicated Server | `bin/win64/vphysics.dll` | `bin/vphysics.dll` | not available yet | `bin/vphysics_srv.so` |
   | Source SDK Base 2013 Multiplayer | `bin/x64/vphysics.dll` | `bin/vphysics.dll` | `bin/linux64/vphysics.so` | `bin/vphysics.so` |
   | Source SDK Base 2013 Dedicated Server | `bin/x64/vphysics.dll` | `bin/vphysics.dll` | `bin/linux64/vphysics_srv.so` | `bin/vphysics_srv.so` |

3. Start the game or server and run `vbox_version` in the console. It prints the module version, the Box3D revision and the build platform. If the command is unknown, the original module is still loaded.

Steam puts the original file back whenever it verifies or updates the game, so copy the module over again after each update.

## Configuration

| Name | Default | Description |
|:--|:--|:--|
| `vbox_threads` | `0` | Physics worker threads, from 0 to 32. `0` uses one per physical CPU core. Saved to the config; takes effect on the next map load. |
| `vbox_version` | | Command. Prints the module version, the Box3D revision and the build platform. |

## Features

Compared with the original VPhysics and with [Volt](https://github.com/misyltoad/VPhysics-Jolt) (VPhysics on Jolt):

| Feature | VPhysics | Volt | VBox3D |
|:--|:--:|:--:|:--:|
| Constraints (except pulleys) | ✔️ | ✔️ | ✔️ |
| Pulleys | ✔️ | ✔️ | ✔️ |
| Breakable constraints | ✔️ | ❌ | ✔️ |
| Motion controllers | ✔️ | ✔️ | ✔️ |
| Constraint motors | ✔️ | ✔️ | ✔️ |
| Ragdolls | ✔️ | ✔️ | ✔️ |
| Triggers | ✔️ | ✔️ | ✔️ |
| Object touch callbacks | ✔️ | ✔️ | ✔️ |
| Prop damage / breaking | ✔️ | ✔️ | ✔️ |
| Fluid events | ✔️ | ✔️ | ✔️ |
| Prop splashing effects | ✔️ | ✔️ | ✔️ |
| Wheeled vehicles | ✔️ | ✔️ | ✔️ |
| Raycast vehicles (airboat) | ✔️ | ❌ | ✔️ |
| Shadow controllers (NPCs, doors) | ✔️ | ✔️ | ✔️ |
| Save / restore | ✔️ | ✔️ | ✔️ |
| Per-object no-collide callbacks | ✔️ | ✔️ | ✔️ |
| Crash-resistant solver | ❌ | ✔️ | ✔️ |
| Thousands of objects without lag | ❌ | ✔️ | ✔️ |
| Multithreaded | ❌ | ✔️ | ✔️ |
| Player controller | ✔️ | ✔️ | ✔️ |

## Building

You'll need:

* **CMake** 3.25 or newer
* **Windows:** Visual Studio 2026 with the **Desktop development with C++** workload. The `windows-x86` preset also needs **C++ Clang tools for Windows**, because Box3D is built with ClangCL for 32-bit.
* **Linux:** Podman or Docker. Linux builds run inside Valve's Steam Runtime SDK, the same environment the games are built in.

Clone the repository with its submodules (Box3D and the Source SDK 2013):

```
git clone --recursive https://github.com/Avrena/VPhysics-Box3D.git
cd VPhysics-Box3D
```

For an existing clone, run `git submodule update --init --recursive`.

On Windows, configure and build the preset for your platform:

```
cmake --preset windows-x64
cmake --build --preset windows-x64-release
```

On Linux, run the same two steps in the Steam Runtime container (use `docker` in place of `podman` if you prefer):

```
podman run --rm -v "$PWD":/src -w /src registry.gitlab.steamos.cloud/steamrt/sniper/sdk:latest \
	sh -c "cmake --preset dedicated-linux-x86 && cmake --build --preset dedicated-linux-x86-release"
```

The library ends up in `build/<preset>/src/Release/`. Use the `-debug` build presets for a debug build, which goes to `Debug/` instead. Linux builds also write a separate `.so.debug` symbol file next to the library.

The version that `vbox_version` prints comes from `git describe`, so build from a git checkout.

## Reporting problems

Report bugs on the [upstream issue tracker](https://github.com/Asphaltian/VPhysics-Box3D/issues). It is for crashes and for behaviour that differs from the original VPhysics. Include:

* the `vbox_version` output
* the game, branch and platform
* whether the problem also happens with the game's original `vphysics`

## Media

[![Jolt vs. Box3D](https://i.ytimg.com/vi/okKg7In3OJw/hqdefault.jpg)](https://www.youtube.com/watch?v=okKg7In3OJw "Jolt vs. Box3D")

More videos:

* [IVP/Havok vs. Jolt vs. Box3D](https://youtu.be/cs8C8T1vg7g) (older version)
* [Watermelons](https://youtu.be/yTp4jTYFWJ8)
* [Buoyant Mossmans](https://youtu.be/z4MaxT87Eqs)
* [Battle for Flatgrass](https://youtu.be/BVPiprozkNM)
* [Vehicular Homicide](https://youtu.be/IOlv3qxC7Js)

## Credits

* [VPhysics Box3D](https://github.com/Asphaltian/VPhysics-Box3D) by Asphaltian
* [Box3D](https://github.com/erincatto/box3d) by Erin Catto
* Inspired by [Volt](https://github.com/misyltoad/VPhysics-Jolt) by Joshua Ashton and Josh Dowell

## License

MIT, see [LICENSE](LICENSE). Box3D, qhull and Source SDK code retain their respective licenses.
