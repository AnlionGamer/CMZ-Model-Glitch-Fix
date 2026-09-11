# Model Glitch Fix v1.0.0 — Technical Notes

## Root Cause

Castle Miner Z's shared `DNA.Drawing.SkinnedModelEntity` path builds skin transforms using world-space bone transforms that already include `LocalToWorld`, while the normal effect setup receives an identity `World` matrix.

At sufficiently large absolute world coordinates, 32-bit floating-point precision cannot retain the same small positional differences between closely layered skinned surfaces. Movement makes the quantization visible as flicker or apparent penetration between layers.

The player armor and helmet make the vanilla defect particularly visible because they sit close to underlying player geometry. Some custom enemy appearances can expose susceptible layered geometry that base enemy appearances do not make obvious.

## Runtime Correction

For compatible effects implementing `Microsoft.Xna.Framework.Graphics.IEffectMatrices`, v1.0.0 temporarily transforms each skin matrix as:

`localSkin = worldSkin * Invert(LocalToWorld)`

After Castle Miner Z populates the effect, the mod replaces the vanilla identity `World` value with the same `LocalToWorld` matrix.

The original `_skinTransforms` values are restored immediately afterward. Unsupported effect types remain on the vanilla path and generate at most one warning per effect type.

## Why This Helps

The correction leaves the intended final pose and world position effectively unchanged, but it keeps the small local-space geometric differences between layered surfaces numerically well-behaved for longer. The large absolute world translation is applied through the normal `World` matrix instead of being baked into every skinned bone matrix before rendering.

## Scope

This is a render-path correction only. It does not rebase gameplay coordinates and does not intentionally modify networking, collision, animation state, textures, lighting, depth bias, rasterizer configuration, save data, or enemy behavior.

## Client-Side Behavior

The correction affects only what the local game client renders. In multiplayer, each player who wants the correction should install Model Glitch Fix locally. The mod does not add packets, change the multiplayer protocol, or require unmodded clients to install it.

## Compatibility Target

Castle Miner Z 1.9.9.8. The v1.0.0 builder validates the target installation against pinned SHA-256 hashes for `CastleMinerZ.exe` and `DNA.Common.dll` before producing the package.

## v1.0.0 Test Result

The final v1.0.0 build was tested in-game and continued to correct the intended defect. Player armor and helmet flicker/bleed-through remained corrected at distance, and the affected custom enemy appearances tested no longer visibly flickered.
