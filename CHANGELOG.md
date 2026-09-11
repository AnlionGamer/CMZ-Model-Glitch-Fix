# Changelog

All notable public changes to **Model Glitch Fix** are documented here.

## v1.0.0 — 2026-09-11

Initial public release.

### Fixed

- Corrected the vanilla far-from-origin skinned-model precision defect that causes player body armor to flicker or reveal the character texture underneath.
- Corrected the corresponding helmet flicker/bleed-through seen at large world coordinates.
- Corrected the same shared rendering artifact when susceptible custom enemy appearances expose layered geometry.

### Technical

- Factors `LocalToWorld` out of compatible skinned bone matrices for draw-time effect setup.
- Restores `LocalToWorld` through the effect `World` matrix.
- Restores the original skin-transform array immediately after effect parameter setup.
- Leaves unsupported effect types on the vanilla rendering path instead of force-patching them.
- Does not intentionally change textures, lighting, depth bias, gameplay coordinates, collision, animation state, enemy behavior, save data, or multiplayer protocol.

### Multiplayer

- Client-side only.
- Each client independently receives the visual correction when the mod is installed locally.
- Vanilla clients remain compatible.
