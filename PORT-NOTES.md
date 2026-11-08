# Eclipse Shader — Horizon Port

Port of [Eclipse-Shader](https://github.com/Merlin1809/Eclipse-Shader) (branch `Unstable`, a Bliss / Chocapic13 edit) for [Horizon](https://github.com/horizon-rust/horizon), the Rust Minecraft rewrite. Horizon nightly builds load Iris-format shader packs through their own clean-room GLSL translator.

## Changes vs the original pack

1. `shaders/shaders.properties`: removed `FADE_VARIABLE` from `iris.features.optional`. Horizon's translator does not provide the `mc_chunkFade` attribute, which broke every gbuffers program built on `dimensions/all_solid.vsh` / `all_translucent.vsh` (chunk fade-in is lost; chunks pop in normally).
2. Deleted the geometry-stage stubs `gbuffers_terrain.gsh` and `gbuffers_beaconbeam.gsh` in `world0`, `world-1`, `world1`, `world0_with_aether_flag`, `world0_with_twilight_forest_flag`. Horizon supports geometry shaders only for the shadow pass, so these programs were skipped entirely.

Nothing else is modified. Tessellation stays off (the pack's own default).

## Verification

Tested with Horizon's official compatibility harness (`crates/horizon-shaders/tests/real_packs.rs`) against Horizon main:

- Original pack: composite programs 51/51 translated, but 12 gbuffers programs skipped (terrain, terrain_cutout, water, entities, block across all 3 dimensions).
- This port: **0 programs skipped**, all dimensions load their real `gbuffers_terrain`, `gbuffers_water`, `gbuffers_entities`, `gbuffers_block` programs, shadow pass works, composite 51/51.

Known minor gap: the biome custom uniforms `isSwamps`, `isJungles`, `isDarkForests`, `isPaleGarden` read 0 (Horizon does not expose `biome` / `biome_category` to custom uniforms yet), so biome-specific tinting tweaks do not trigger.

## Install

Use a Horizon nightly build. Drop this pack (folder or zip with `shaders/` at the root) into the shader packs folder and select it under Shader Packs.
