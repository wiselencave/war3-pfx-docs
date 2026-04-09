# PopcornFX File Extensions

> ⚠️ This document is a work in progress. Information may be incomplete, inaccurate, or subject to change.

## Effects

### `.pkfx` (text)

Human-readable source format. Used in PK-Editor for authoring effects. Does not work in Reforged.

### `.pkfx` (binary)

Baked binary version of an effect. Produced by PK-Editor or AssetBaker. Works in Reforged.

### `.pkb`

Functionally identical to a baked `.pkfx`. Blizzard's preferred extension for baked effects in Reforged — the exact meaning of the abbreviation is unknown (PK Binary / Baked / Blizzard). Renaming the extension from `.pkfx` to `.pkb` is optional — the game can read both. 

---

## Assets

### `.pkat`

Texture atlas definition file. A simple text file containing a list of UV coordinates , used to describe the layout of a sprite sheet. Works in Reforged.

### `.pkmm`

Baked 3D mesh data. Produced by the editor from source `.fbx` files.  Referenced by effects that use mesh samplers. Works in Reforged.