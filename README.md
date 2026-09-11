# CastleMiner Z — Model Glitch Fix

A client-side rendering fix for **CastleMiner Z 1.9.9.8** that corrects a vanilla far-distance model glitch affecting skinned character models.

> **Unofficial community project:** Model Glitch Fix is independently created and published by AnlionGamer. It is not an official CastleMiner Z release and is not affiliated with, sponsored by, approved by, or endorsed by the game's developers or publisher.

**Publisher:** AnlionGamer  
**Current release:** v1.0.0  
**Mod ID:** `cmz.model-glitch-fix`

## What It Fixes

CastleMiner Z can lose rendering precision as skinned models move farther from the world origin. The most visible vanilla symptoms are:

- player body armor flickering or revealing the character texture underneath;
- player helmets developing similar flicker/bleed-through;
- the artifact becoming increasingly visible at large world coordinates and especially during movement;
- susceptible custom enemy appearances exposing the same underlying skinned-model defect.

Model Glitch Fix corrects the shared draw-time transform path responsible for the problem.

## v1.0.0 Verified Behavior

The final v1.0.0 build was tested in-game after the correction was developed from a dedicated precision diagnostic.

Observed results:

- player armor and helmet flicker/bleed-through was eliminated at distance;
- affected custom enemy appearances that previously exposed the defect no longer visibly flickered in the tested cases;
- the correction remained effective during movement;
- the issue was confirmed to be a rendering-precision problem rather than a lighting or texture-selection problem.

## How the Fix Works

CastleMiner Z's shared `DNA.Drawing.SkinnedModelEntity` rendering path normally includes the full `LocalToWorld` transform inside the skinned bone matrices and then supplies an identity `World` matrix to the effect.

At sufficiently large absolute world coordinates, 32-bit floating-point precision can no longer preserve the same tiny positional differences between closely layered skinned surfaces. Movement makes the resulting quantization visible as flicker or apparent surface penetration.

For compatible skinned effects, Model Glitch Fix changes only the draw-time representation:

1. `LocalToWorld` is temporarily factored back out of the skin matrices.
2. The same `LocalToWorld` transform is supplied through the effect's normal `World` matrix.
3. The original skin-transform array is restored immediately after effect parameters are populated.

The intended pose and world position remain the same, while small local-space geometry separation is preserved more accurately at large world coordinates.

See [`TECHNICAL_NOTES.md`](TECHNICAL_NOTES.md) for the implementation-level explanation.

## What It Does Not Change

Model Glitch Fix does **not** intentionally modify:

- textures or skin selection;
- lighting;
- depth bias or rasterizer state;
- gameplay/world coordinates;
- collision or physics;
- animation state;
- enemy behavior;
- save formats;
- multiplayer packets, protocol, or replicated gameplay state.

## Requirements

- CastleMiner Z **1.9.9.8** (Steam)
- CMZ Mod Manager **1.2.0 or newer**
- CMZ Mod Framework/API **1.0.0 or newer**
- Windows / x86 game process

## Installation

1. Download `CMZ_Model_Glitch_Fix_v1.0.0.cmzmod` from the GitHub Releases page.
2. Install the `.cmzmod` through CMZ Mod Manager.
3. Enable **Model Glitch Fix** in the active profile.
4. Launch CastleMiner Z through the Mod Manager.

## Multiplayer

Model Glitch Fix is a **client-side rendering correction**.

Each player who wants the corrected rendering should install the mod locally. A host having the mod does not automatically alter how an unmodded joining client renders models.

The mod does not add custom multiplayer messages, modify CastleMiner Z's network protocol, change session rules, or require other players to install it. Vanilla clients remain compatible.

## Compatibility and Scope

The v1.0.0 builder targets CastleMiner Z 1.9.9.8 and validates pinned hashes for `CastleMinerZ.exe` and `DNA.Common.dll` before producing the installable package.

The runtime correction is applied at the shared skinned-model rendering layer for compatible effects. Unsupported effect types are left on the vanilla path rather than being force-patched.

## Privacy

- No telemetry or analytics.
- No web requests or data uploads.
- No custom multiplayer traffic.
- No intentional collection or persistence of player identity information.

## Save / Removal Safety

Model Glitch Fix does not intentionally modify world data, player progression, inventories, or save formats. It can be disabled or removed through CMZ Mod Manager without requiring save conversion.

## Repository Policy

This repository is for **public releases and finished public-facing states only**. Development builders, intermediate diagnostics, dependencies, private build artifacts, and generated build outputs are intentionally excluded.

The reference manifest for each public release is kept under `Release/`. Installable `.cmzmod` packages belong on the GitHub **Releases** page rather than in the repository tree. Current release packages carry their applicable license and project notice inside the `.cmzmod` so those terms remain attached when the package is shared separately from GitHub.

## License and Attribution

Model Glitch Fix v1.0.0 and the current repository state are governed by the **AnlionGamer Community Distribution Terms v1.0**. See [`LICENSE`](LICENSE).

The terms allow normal use, source inspection where source is published, and private modification. Public redistribution of the original project, packaged mod, forks, or modified builds requires **prior permission from AnlionGamer** and must remain **non-commercial**. Sale and paid access are prohibited without separate permission.

The original Model Glitch Fix concept, investigation direction, testing direction, and release are credited to **AnlionGamer**. Additional project attribution and the CastleMiner Z rights notice are documented in [`NOTICE.md`](NOTICE.md).

CastleMiner Z and its original game assets remain the property of their respective rights holders. This is an independent, unofficial fan-made mod project and is not affiliated with or endorsed by the game's rights holders.
