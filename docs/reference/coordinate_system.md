# Coordinate System & Axis Convention

> ⚠️ This document is a work in progress. Information may be incomplete, inaccurate, or subject to change.

## Overview

Warcraft III and PopcornFX use different axis conventions. Understanding this mismatch is essential for creating effects that behave correctly in-game.

| Engine | Forward | Left/Right | Up |
|--------|---------|------------|-----|
| Warcraft III (MDX) | +X | +Y (left) | +Z |
| PopcornFX RHZUP | +Y | +X (right) | +Z |

Both systems are Z-Up and right-handed, but the forward and side axes are swapped. This means that `axis.Forward` in PopcornFX points along +Y, while the unit's actual facing direction in Warcraft III is +X. The result: particles fly **behind** the unit instead of in front of it.

## How PopcornFX handles coordinate systems

PopcornFX sets a global coordinate frame at startup via `CCoordinateFrame::SetGlobalFrame()`. Both the Asset Baker and the Warcraft III runtime set this to **frame 1 (RHZUP)** — the standard right-handed Z-Up convention.

The PopcornFX editor additionally supports per-effect coordinate frame overrides through project/effect settings:

```
CoordinateFrame = Custom;
CoordinateFrameAxisX = Right;
CoordinateFrameAxisY = Backward;
CoordinateFrameAxisZ = Up;
```

These settings tell the compiler that the +Y axis in the incoming transform data actually corresponds to `Backward`, which causes `axis.Forward` to return `-Y` — matching the real unit facing direction.

## Quick Bake vs Asset Baker

### Quick Bake (editor)

The PopcornFX editor reads per-effect coordinate frame settings and applies them during compilation. With the custom axis configuration above, `axis.Forward`, `scene.axisForward`, and related nodes return correct directions matching the Warcraft III transform space.

**Quick Bake respects your axis settings. Effects work as expected.**

### Asset Baker

The Warcraft III Asset Baker (`PopcornAssetBaker.exe`) hardcodes `SetGlobalFrame(1)` (standard RHZUP) during startup and never reads or applies per-effect coordinate frame overrides. The bake config file (`Popcorn/AssetBaker.pkcf`) does not contain coordinate system settings, and the baker does not process `CoordinateFrame`/`CoordinateFrameAxisX/Y/Z` properties from effects.

**The Asset Baker ignores your axis settings. `axis.Forward` will point in the wrong direction.**

## The scale factor

The Warcraft III runtime applies a scale of **100×** when initializing PopcornFX (`pkSystem::SetScale(100.0)`). This means 1 Warcraft III world unit = 100 PopcornFX units. Keep this in mind when authoring positions, velocities, and distances — values that look correct in the editor may appear 100× too large or too small in-game, depending on the context.

## Recommendations

### Use axis nodes, not hardcoded vectors

Always use `scene.axisForward`, `scene.axisSide`, and `scene.axisUp` instead of hardcoded `float3` vectors. These nodes resolve to the correct directions based on the active coordinate frame, making your effects axis-system independent.

This applies to both directions and positions. To build an arbitrary offset, combine the axis nodes with scalar multipliers:

```
float3 offset = scene.axisForward * 2.0 + scene.axisUp * 1.5;
```

### If using Quick Bake

Set the following in your effect or project settings:

```
CoordinateFrame = Custom;
CoordinateFrameAxisX = Right;
CoordinateFrameAxisY = Backward;
CoordinateFrameAxisZ = Up;
```

With these settings, all axis nodes (`axis.Forward`, `scene.axisForward`, etc.) will return correct directions. This is the recommended workflow.

### If using the Asset Baker

The Asset Baker does not apply per-effect axis overrides. You have two options:

**Option A — Invert manually in the effect script:**

Replace axis node usage with inverted equivalents:

| Desired direction | Instead of | Use |
|-------------------|------------|-----|
| Forward (unit facing) | `axis.Forward` | `-axis.Forward` |
| Right (unit's right) | `axis.Right` | `-axis.Right` |
| Up | `axis.Up` | `axis.Up` (no change) |

This approach works with both the Asset Baker and the runtime, but makes effects dependent on a specific axis convention.

**Option B — Patch the Asset Baker binary:**

Modify the baker to call `CCoordinateFrame::SetupFrame()` with the correct axis mapping before baking, mirroring what the editor does during Quick Bake. This is a binary modification and may need to be reapplied after baker updates.

## Technical details

The full coordinate frame enum used internally by PopcornFX:

| Value | Name | Description |
|-------|------|-------------|
| 0 | RHYUP | Y-Up, right-handed |
| 1 | RHZUP | Z-Up, right-handed |
| 2 | LHYUP | Y-Up, left-handed (Unity) |
| 3 | LHZUP | Z-Up, left-handed (UE4) |
| 4 | User1 | Custom axes via `SetupFrame()` |
| 5 | User2 | Custom axes via `SetupFrame()` |

The Warcraft III runtime initializes PopcornFX with frame 1 (RHZUP) and a scale of 100. The Asset Baker uses the same frame. Neither applies per-effect axis overrides at the `SetGlobalFrame` level — the editor handles this through a separate per-effect compilation path that the standalone baker lacks.