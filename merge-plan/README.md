# Merge plan — GTA SA Android

## Decision for the requested game build

**Do not install the supplied AML/CLEO/SA_DOX plugin stack into GTA San Andreas Android 2.11.311 (the arm64-v8a build).** The archive audit found 20 native .so entries and every one is ELF32 / ARM (32-bit). No AArch64 / arm64-v8a plugin build is included. A 32-bit library cannot be loaded into a 64-bit game process.

This blocks the runtime-dependent parts of the requested merge:
- AML loader and AML plugins
- CLEO runtime and the 25 supplied CLEO scripts
- SAUtils
- JPatch / AML Limit Adjuster
- SA_DOX via libSDX.so

This is an architecture gate, not a POCO F6 performance limitation. The device CPU is 64-bit, but the game process ABI is the determining factor.

## Verified archive scan

Source release: [v1.0](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/releases/tag/v1.0)

| Archive | Bytes | SHA-256 |
|---|---:|---|
| GTA V Project Modpack V8 | 1,392,624,735 | 18498a421859d8fb9aff735082b2cf4f5030629d3a76a9b4f78c875363f6dad3 |
| Saadox 2.0 DirectX 3.0 | 140,373,205 | 92ab759f167f11823130e78eff8d26923332e6f7d4188e0fe2eeaea92e556bc0 |

The recursive scan verified and extracted all five nested archives. It indexed 771 files (about 3.08 GB uncompressed), including 20 ELF shared-library entries and 25 CLEO scripts. No nested archive failed extraction.

Full compact report: [deep-inspection run 4](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/actions/runs/41504597074). Run 6 adds IMG indexing and raw ZIP duplicate-entry checks.

## Native plugin audit

All 20 .so entries are **32-bit ARM**. There are no 64-bit ARM plugin binaries in either archive.

V8's 19 shared-library entries:
- libAMLLA.so
- libAcceptEULA.so
- libAdvancedSettings.SA.so
- libBASSMod.so
- libBikeFlipFix.so
- libCLEOMod.so
- libCLEOPlus.so
- libCLEO_AudioStreams.so
- libCLEO_IniFiles.so
- libFullscreenSplash.so
- libGPS.so
- libJPatch.so
- libMoreSettings.so
- libProject2DFX.so
- libSA.BetterFps.so
- libSAPlanter.so
- libSAUtils.so
- libSkyGFXMobile.so
- optional libSDX.so

The standalone Saadox archive adds another libSDX.so, but its SHA-256 is identical to the V8 optional copy:
a8fa352011c8ec4a21985ec0d006da1e5943048a96705126c6e1cf5f094d8f13.
Only one copy would be needed on a supported 32-bit base.

The V8 AMLLA binary contains version-module strings for GTA SA 2.00 and 2.10 32-bit; there is no 2.11.311 module string. The official AML Plugin SDK documents GTA SA v2.00 and v2.10 (64-bit), not 2.11.311. SAUtils source has separate logic for 2.00 (32-bit) and 2.10, but no 2.11.311 branch. Do not infer 2.11 compatibility from the fact that the mod can be unpacked.

## CLEO

V8 includes 25 scripts: 12 .csa and 13 .csi. The GTA V-style subset includes:
- GTA V Hud.csa
- Gta V Money.csa
- Gta V Phone.csa
- Aim GTA 5.csa
- Radio.Wheel.csa
- gtasa.Weapon_Wheel_3.csa
- gtasa.Weapon_Wheel_Control.csa

Other supplied scripts cover rain, gas, player lights, vehicles, skins, apparel, weapons, cheats, missions, passengers, weather, teleport and quick-save.

Keep these files in the archive record; do not delete them from the source set. However, they will not execute in 2.11.311 using the included 32-bit CLEO library. An ARM64 CLEO project found during the audit is still alpha and its README explicitly says script execution and opcode interpretation are not implemented yet.

SA_DOX's Android compatibility guide names a list of incompatible CLEO scripts. None of the V8 script filenames exactly matches that list. The two Taillight Headlight scripts are semantically close to the guide's “2DFX Taillights” warning, but their compiled code was not decompiled, so mark them **test / investigate**, not confirmed incompatible.

## SA_DOX / Saadox preset decision

The standalone Saadox RAR contains four nested ZIP presets:
- Low Quality
- Medium Quality
- High Quality
- Ultra

Each preset has 80 files. Across the four extracted presets, only two files differ:
- SA_DOX/Advanced.ini
- SA_DOX/shaderUniform.ini

The other 78 files are identical across presets.

### Preferred preset if moved to a supported base

**Ultra** is the graphics-priority profile:
- render resolution scale: 75
- bloom: enabled
- god-ray resolution: 512
- cascaded shadow maps: 2048 / 1024 / 1024
- SSR: enabled
- SSAO: enabled
- volumetric cloud maxStep: 80

High uses 1024 / 1024 / 1024 cascaded shadows and volumetric maxStep 50. It is the sensible fallback if Ultra's frame pacing is poor. The actual performance of these presets has not been tested on the phone.

### Do not overlay the V8 optional SA_DOX folder

V8 also includes a separate optional SA_DOX folder. Its advanced.ini is not the Saadox Ultra config: it uses resolution scale 70, bloom off, a different god-ray setting, and shadow map values of 4096 / 2048 with the third cascade disabled. Its SA_DOX/data/timecyc.dat differs from the standalone Saadox presets. The standalone presets share one identical timecyc.dat and one identical colorcycle.dat.

For a supported runtime, select the **complete standalone Saadox Ultra SA_DOX folder** and do not merge its config files with the older V8 optional SA_DOX folder. The duplicate libSDX.so files are identical; only one is needed.

The Saadox Android installation note itself tells AML users to copy the selected SA_DOX folder into the game's files directory and libSDX.so into the mods directory. Its separate Android tutorial says to use an AML 2.00 APK and requires AML Limit Adjuster plus JPatch.

## Explicit SA_DOX plugin conflicts in V8

The SA_DOX Android guide lists these AML plugins as incompatible. They are present in V8 and should be disabled if SA_DOX is used on a supported base:
- libGPS.so
- libMoreSettings.so
- libProject2DFX.so
- libSAPlanter.so
- libSkyGFXMobile.so

Do not remove CLEO wholesale or remove SAUtils merely because these five plugins conflict. AML Limit Adjuster and JPatch are listed as SA_DOX requirements, but their supplied V8 binaries are 32-bit and still cannot load into 2.11.311.

## V8 data-file conflicts and selections

- data/popcycle.dat: the main V8 copy and “Busy traffic cheat” copy have identical content. “Low traffic cheat” is different. Install only one. Busy is the realism-first option; Low is the performance fallback.
- V8 has two mainV1.scm copies with identical content but different paths. Do not combine different versions over them.
- scriptv1.img is supplied at two paths in V8. Keep the path layout from the chosen source rather than flattening the folders.
- V8 also contains handling.cfg, handlingpc.cfg, ped.ifp and SALodLights.dat; these are not supplied by the standalone Saadox presets.
- V8's normal game data/timecyc.dat and data/colorcycle.dat are separate from the SA_DOX folder versions. Do not copy an SA_DOX timecycle into the normal game data path.

## What can actually be merged now?

For **2.11.311**, no safe ready-to-install merged AML/CLEO/SA_DOX package can be made from these archives. Doing so would package 32-bit native libraries for a 64-bit game process and produce non-loading plugins or crashes.

The following work remains valid for a separate, documented 32-bit AML game build: preserve all 25 CLEO scripts in the archive, keep SAUtils, AMLLA and JPatch, use the standalone Saadox Ultra folder, disable the five SA_DOX-conflicting V8 plugins, and choose one traffic preset. This is **not** an instruction to replace the user's 2.11.311 installation.

For the requested 2.11.311 target, the path forward is to obtain or build ARM64 versions of the required AML loader, SAUtils, JPatch, CLEO runtime, and SA_DOX libSDX.so that explicitly support game build 2.11.311. The supplied archives contain none.

## Sources

- [Rockstar: GTA SA mobile update notes](https://support.rockstargames.com/articles/KlYrReNNBKJIkCUhUb0bR/grand-theft-auto-san-andreas-mobile-title-update-notes-android-and-ios) — Android v2.11.311 release note.
- [GTA SA 2.11.311 technical extraction guide](https://github.com/korewaChino/gtasa_linux/blob/main/ASSET_PREPARATION.md) — identifies the Play Store build as arm64-v8a.
- [Android NDK ABI guide](https://developer.android.com/ndk/guides/abis) — distinguishes armeabi-v7a from arm64-v8a.
- [AndroidModLoader AML Plugin SDK](https://github.com/AndroidModLoader/aml-psdk) — documented game support is 2.00 and 2.10 64-bit.
- [AndroidModLoader SAUtils source](https://github.com/AndroidModLoader/SAUtils) — version-specific initialization branches.
- [SA_DOX Android compatibility / installation guide](https://cheeseburgersmod.blogspot.com/2025/02/how-to-use-properly-on-sadox-20-android.html) — incompatible plugins, scripts and required plugins.
- [Saadox 2.0 DirectX 3.0 package page](https://www.gtainside.com/en/sanandreas/mods/219642-sa-do-x-2-0-in-directx-3-0-graphics-style) — four quality presets and graphics goals.


## Deep inspection run 6 — completed

[GitHub Actions run 6](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/actions/runs/41505606444) completed successfully. Its report artifact is `gta-sa-deep-inspection-6` (520,870 bytes; available through 2026-12-02).

Verified scan results:
- 771 files indexed, 3,083,717,961 uncompressed bytes.
- All 5 nested archives tested and extracted successfully.
- 20 shared-library entries, all ELF32 / ARM.
- 25 CLEO scripts: 12 .csa and 13 .csi.
- 9 GTA IMG VER2 archives parsed, 17,157 directory entries, no duplicate names inside any IMG.
- 6 ZIP archives inspected, no duplicate member paths and no encrypted members.
- 96 identical-SHA duplicate groups.
- 81 normalized target-path collision groups: 55 exact-content duplicates and 26 different-content alternatives. The 26 alternatives are 25 SA_DOX paths (V8 optional SA_DOX versus standalone SA_DOX) plus the single data/popcycle.dat traffic-density choice. The SA_DOX differences are expected alternatives, not 25 independent game-data conflicts.

### GTA V-style menu option

V8 includes a separate optional folder named `Menu GTA V Style` containing 11 files under `texdb/menu/`, including `menu.txt` and the DXT / ETC / PVR texture database files. Its three `.dat` variants are byte-identical, as are the `.tmb` and `.toc` variants. Keep the complete variant set intact unless a target-game texture-loader test proves that a format can be omitted. This menu pack is the clearest non-CLEO GTA V-style menu asset in the archive; it is still untested on 2.11.311.

### IMG archive contents

All nine V8 IMG archives passed VER2 directory parsing:
- `anim/anim.img`: 133 animation entries.
- `anim/cuts.img`: 444 cutscene entries (148 .ifp, 148 .cut, 148 .dat).
- `data/paths/carrec.img`: 426 vehicle-record entries.
- The two `scriptv1.img` copies each contain 79 .scm files and are byte-identical (SHA-256 `146358135eadab2e09cc4d9877a8366552b575cb482e4f667935899e14ac7954`).
- `texdb/cutscene.img`: 317 entries.
- `texdb/gta3.img`: 13,557 entries (12,964 .dff, 216 .col, 164 .ipl, 149 .ifp, 64 .dat).
- `texdb/gta_int.img`: 1,967 entries.
- `texdb/player.img`: 155 entries.

Every parsed IMG had zero duplicate internal entry names. This is a structural check, not a comparison against an untouched 2.11.311 base game. Without a matching clean base archive, we cannot prove which of the 17,157 entries were modified versus original game content.

### 2.11.311 port status

A public community source snapshot for GTA SA 2.11.311 says its current work only redirects the game storage location to Android/media and explicitly hopes AML, CLEO and FASTMAN can be ported in the future. It does not provide a working AML/CLEO port. Separately, the ARM64 LuJimCLEO64 v0.2.0-alpha README says script execution, opcode interpretation, CLEO4/CLEO5 runtime and native CLEO plugins do not work yet.

Therefore the architecture blocker is not solved by an available ARM64 CLEO replacement. Do not substitute an experimental scanner or an unofficial 2.10 package for the user's target version.

### Shader implementation comparison

The V8 optional SA_DOX copy is not interchangeable with the standalone graphics archive. Its `downsamplebloom.shader` and `upsamplebloom.shader` are no-op placeholders: the vertex stage sets `gl_Position` to an off-screen coordinate and the fragment stage discards. The standalone presets contain working multi-sample bloom shader code at the corresponding paths. This confirms the standalone package is the better visual base; do not overlay the V8 optional shader directory on it.

The V8 optional folder's other shader files also differ at the same normalized SA_DOX paths. These are expected alternatives, not individual files to cherry-pick. Select one complete SA_DOX folder, not a hybrid.

### SAUtils architecture branch detail

The current upstream SAUtils Android.mk builds a separate `SAUtils64` library for non-`armeabi-v7a` builds, and its source dispatches the 64-bit case to the GTA SA 2.10 initialization path. This is evidence of 2.10 64-bit support, not 2.11.311 support. The V8 archive contains only its 32-bit `libSAUtils.so`. See [SAUtils Android.mk](https://github.com/AndroidModLoader/SAUtils/blob/main/Android.mk) and [SAUtils main.cpp](https://github.com/AndroidModLoader/SAUtils/blob/main/main.cpp).
