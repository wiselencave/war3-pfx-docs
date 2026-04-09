# Warcraft III Reforged — Simulation Interfaces

> ⚠️ This document is a work in progress. Information may be incomplete, inaccurate, or subject to change.

## What are simulation interfaces?

Simulation interfaces are a PopcornFX SDK feature that allows the host engine to bind native C++ functions directly into the particle nodegraph. Unlike attributes (which pass simple values into an effect), simulation interfaces replace entire graph nodes with engine-side logic at runtime.

In PK-Editor, a simulation interface appears as a regular template graph with a default implementation. When the effect runs inside the game engine, the engine can override that default with its own native code. This means the graph you see in the editor is just a fallback — the actual behavior at runtime may be completely different.

Simulation interfaces are documented in the "Simulation Interfaces" section of the PopcornFX C++ SDK documentation (requires an SDK license).

## HasTeleported

Warcraft III Reforged registers one known simulation interface: `War3.HasTeleported_SI`.

It is defined in `Popcorn/Library/Templates/War3.pkfx` — a template library that is likely part of Blizzard's internal PopcornFX workflow for Reforged. It outputs a single `bool` stream called `HasTeleported`. The engine uses it to detect when an object moves more than 200 units between frames (i.e. a teleport rather than smooth movement). When this triggers, the effect instance is immediately destroyed — presumably to avoid long-running trails or ribbons stretching across the map.

This interface has been found referenced in baked effects such as `wisp.pkb` and `spiritofvengeance.pkb`.

### Usage

It is unclear whether custom effects can use this simulation interface from PK-Editor, since simulation interfaces are an SDK-level feature.