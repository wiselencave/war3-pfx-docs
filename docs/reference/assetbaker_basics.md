# PopcornAssetBaker.exe

> ⚠️ This document is a work in progress. Information may be incomplete, inaccurate, or subject to change.

`PopcornAssetBaker.exe` is a command-line tool used internally by Blizzard to bake PopcornFX particle effects into binary `.pkfx`/`.pkb` files. It was accidentally leaked as part of Warcraft 3 development software in December 2024.
 
The tool is built on top of [PK-AssetBakerLib](https://documentation.popcornfx.com/PopcornFX/v2.5/baking/index.html) — the baking library from the PopcornFX SDK that handles converting source effects and resources into runtime-ready binaries. In PopcornFX v2.x, the AssetBaker is part of the paid SDK and is no longer included with the free PK-Editor. Blizzard's version wraps this library with a simplified CLI that differs from the standard AssetBaker command-line interface — it exposes only five flags instead of the full set of SDK options.
 
The tool works and produces valid `.pkb` files in most cases. Note that using this utility is **not required** — the built-in "Quick Bake" function in the PopcornFX editor achieves the same result.
 
## Basics
 
### CLI Arguments
 
| Flag | Type | Required | Description |
|------|------|----------|-------------|
| `-i <path>` | string | yes | Input directory |
| `-o <path>` | string | yes | Output directory |
| `-s` | flag | no | Recurse through subdirectories |
| `-q` | flag | no | Quiet mode |
| `-v` | flag | no | Verbose mode |
 
### Config
 
The input directory must contain a config file at `Popcorn/AssetBaker.pkcf`.
 
Example contents:
 
```
Version = 2.5.1.63447;
CProjectSettingsBaking    $D857A09F
{
    PlatformSettingsList = {
        "x64:Builded",
    };
    BuildVersions = {
        "PC: desktop, windows",
    };
}
```
 
> More settings may be needed depending on your project — custom configurations has not been tested deeply.
 
Unlike the standard PopcornFX AssetBaker, which exposes a rich set of CLI options, Blizzard's version only accepts the five flags listed above. All baking customization — such as resource oven settings, compiler switches, and platform targeting — is driven entirely through this config file. The config can contain additional objects that configure individual resource ovens (particles, textures, texture atlases, etc.).
 
For reference on what settings the underlying AssetBaker supports, see the PopcornFX baking documentation:
 
- [Baking overview](https://documentation.popcornfx.com/PopcornFX/v2.5/baking/index.html)
- [Particle oven](https://documentation.popcornfx.com/PopcornFX/v2.5/baking/resource-ovens/oven-particle.html)
- [Texture oven](https://documentation.popcornfx.com/PopcornFX/v2.5/baking/resource-ovens/oven-texture.html)
- [Base oven settings](https://documentation.popcornfx.com/PopcornFX/v2.5/baking/resource-ovens/oven-base.html)
 
How much of this is actually functional in the leaked build has not been fully explored.
 
### Library
 
The baker expects to find the `PopcornFXCore` library at `<inputdir>/Popcorn`. Typically a `Library` directory is created automatically in the project folder, so the library needs to be copied to `<inputdir>/Popcorn`.
 
### Boilerplate Project
 
Instead of setting up the config and library manually, you can use a ready-made boilerplate project: [war3-pfx-boilerplate](https://github.com/wiselencave/war3-pfx-boilerplate). It is a pre-configured PopcornFX 2.5 project for Warcraft III: Reforged that already includes the correct `AssetBaker.pkcf` and the relocated `PopcornFXCore` library.
