# GTA SA Mod Audit

Verified binary audit workspace for the supplied Android mod archives.

## Target setup

- Requested game build: GTA San Andreas Android **2.11.311**
- Device: POCO F6
- Priorities: GTA V-style HUD/presentation and high-fidelity graphics.
- Preserve: CLEO scripts and SAUtils in the source archive record.
- Exclusion: the separate 100-CLEO menu previously rejected by the user.

## Current result

**STOP: do not install the supplied AML/CLEO/SA_DOX stack into 2.11.311.**

The game build is identified as arm64-v8a. All 20 native shared-library entries extracted from V8 and Saadox are **32-bit ARM**; no 64-bit ARM plugin is included. This prevents the supplied AML, CLEO, SAUtils, JPatch and SA_DOX runtime libraries from loading in the requested 64-bit game process.

The archives have been downloaded, verified, recursively extracted and hash-indexed by GitHub Actions. The full decision record is in [merge-plan/README.md](merge-plan/README.md).

## Archive assets

- [Release v1.0](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/releases/tag/v1.0)
- [Latest workflow runs](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/actions)
- [Deep-inspection run 6](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/actions/runs/41505606444)

The audit indexes file hashes, nested archive extraction, .so ELF architecture and dependencies, CLEO script names/strings, configurations, duplicate contents, target-path collisions, and (in the newest workflow revision) GTA IMG entries and raw ZIP central-directory duplicates.

## Important merge findings

- 25 CLEO scripts in V8 (12 .csa, 13 .csi); retained in the audit, not runtime-compatible with the requested build through the supplied 32-bit CLEO binary.
- The Saadox archive contains Low, Medium, High and Ultra presets; all share the same 78 of 80 files. Only Advanced.ini and shaderUniform.ini differ.
- Saadox Ultra is the visual-priority preset for a compatible base. Do not combine its configuration with V8's separate optional SA_DOX folder.
- SA_DOX's published Android guide warns against V8's GPS, MoreSettings, Project2DFX, Planter and SkyGFXMobile plugins.
- The only non-SA_DOX target collision is data/popcycle.dat: main/Busy are identical; Low traffic is a different alternative. The other different-content collision groups are the V8 optional SA_DOX variant versus the standalone presets.
- All nine V8 IMG archives were parsed (17,157 entries; no duplicate IMG names). Six ZIP archives had no duplicate member paths. Full installation and in-game performance testing have not been performed.

## Redistribution

Only redistribute files with permission from their authors. Keep source archives private if their licenses or permissions do not allow public mirroring.


## Detailed reports

- [Compatibility matrix for GTA SA 2.11.311](audit/compatibility-matrix.md)
- [Full merge plan and conflict decisions](merge-plan/README.md)
- [Deep inspection run 6 and reports](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/actions/runs/41505606444)
