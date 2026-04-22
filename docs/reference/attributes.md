# Warcraft III: Reforged — PopcornFX Runtime Attributes

> ⚠️ This document is a work in progress. Information may be incomplete, inaccurate, or subject to change.

## What are attributes?
 
Attributes are PopcornFX's mechanism for passing data from the host application into a particle effect at runtime. You declare an attribute in your effect inside PK-Editor (as an exported node with a specific name and type), and the game engine populates it automatically each frame.
 
In practice, this means you can make your effects react to in-game state — for example, tinting particles with the player's team color, or scaling emission rate based on the model's animation data.
 
To use an attribute, add an `Attribute` input node to your effect's nodegraph, set its `Exported Name` to the exact name from the table below (including the prefix), and match the `Exported Type`. The game will recognize the name and feed the corresponding value at runtime.
 
## Attribute list
 
| Attribute | Type | Notes |
|-----------|------|-------|
| `Game.LifespanMultiplier` | float | Lifespan multiplier set on the Popcorn emitter node in the MDX model. Can be animated. Default 1.0. |
| `Game.EmissionRateMultiplier` | float | Emission rate multiplier set on the Popcorn emitter node in the MDX model. Can be animated. Default 1.0. |
| `Game.SpeedMultiplier` | float | Speed multiplier set on the Popcorn emitter node in the MDX model. Can be animated. Default 1.0. |
| `Game.ColorMultiplier` | float4 | Color (RGB) and alpha multipliers set on the Popcorn emitter node in the MDX model. Can be animated independently. |
| `Game.TeamColor` | float4 | RGBA color derived from the Replaceable ID 1 (team color) texture, normalized to 0.0–1.0. See below. |
| `Game.TargetPosition` | float3 | Lightning destination point in world space. Default (0, 0, 0) for regular emitters.  |
| `Game.Scale` | float | Not yet tested. |
| `Weather.TileCenter` | float3 | Not yet tested. |
| `Weather.Size` | float2 | Not yet tested. |
| `Weather.EmissionRate` | float | Not yet tested. |
 
Attributes prefixed with `Game.*` are set on regular particle emitters attached to models. Attributes prefixed with `Weather.*` are set on weather effects.
 
## Team Color
 
`Game.TeamColor` is the most well-understood attribute. The game reads the unit's replaceable texture color (Replaceable ID 1, which corresponds to team color in Warcraft III models) and passes it to the effect as a `float4` with each channel normalized from 0–255 to 0.0–1.0.
 
This is used in, for example, the hero glow effect (`sharedfx/hero_glow/hero_glow.pkb`).
 
## Boilerplate
 
The [war3-pfx-boilerplate](https://github.com/wiselencave/war3-pfx-boilerplate) project includes ready-to-use template nodes (`War3Game` and `War3Weather` in `Library/Wiselen/Templates/War3.pkfx`) that expose all of these attributes with the correct names and types. Just add the node to your effect's nodegraph and wire the output pins — no manual setup needed.