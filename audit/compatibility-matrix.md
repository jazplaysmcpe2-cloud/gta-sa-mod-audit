# Compatibility matrix — requested target: GTA SA Android 2.11.311

This matrix separates the contents that exist in the archives from components that can actually run on the requested game build.

| Component | Present in archive | Verified condition | Decision for 2.11.311 |
|---|---|---|---|
| AML / AMLLA | Yes | Supplied native binaries are ELF32 ARM; AMLLA strings name GTA SA 2.00 and 2.10 32-bit modules | **Blocked** |
| CLEO runtime / CLEOPlus | Yes | Supplied libraries are ELF32 ARM | **Blocked** |
| CLEO scripts | 25 scripts: 12 .csa, 13 .csi | Script files are present and inventoried; runtime cannot load their supplied 32-bit CLEO library | **Preserve, do not delete; execution blocked** |
| SAUtils | Yes | Supplied .so is ELF32 ARM; upstream source has 2.00 / 2.10 branches, no 2.11.311 branch found | **Blocked** |
| JPatch | Yes | Supplied .so is ELF32 ARM | **Blocked** |
| SA_DOX 2.0 DX3 | Four presets | All five nested archives extract; libSDX.so is ELF32 ARM | **Blocked on 2.11.311** |
| GTA V-style menu | Optional V8 folder, 11 files | Texture database files are present; no live test against 2.11.311 texture loader | **Candidate static asset only; unverified** |
| V8 base data / models / IMG / animation | Yes | All 9 IMG VER2 archives parse; no internal duplicate names. No clean 2.11.311 baseline was supplied for binary comparison | **Do not wholesale-overlay; target compatibility unverified** |
| V8 traffic-density choice | Main + Busy + Low | Main and Busy popcycle.dat are SHA-identical; Low is different | **Choose one only; Busy is realism-first candidate** |
| V8 optional SA_DOX | Yes | 25 normalized SA_DOX paths differ from standalone Saadox; includes no-op bloom shader files | **Do not overlay with standalone SA_DOX** |

## Required runtime architecture

The supplied package has 20 .so entries and all are 32-bit ARM. The requested Play Store 2.11.311 process is arm64-v8a. Android's loader cannot load an ELF32 ARM library inside an AArch64 process. This is independent of the phone's CPU speed or GPU capability.

A public community source snapshot for game 2.11.311 says AML, CLEO and FASTMAN would need to be ported and that its current patch only redirects storage to Android/media. It does not supply a working port. The public ARM64 LuJimCLEO64 v0.2.0-alpha is also not a solution: its own README says script execution, opcode interpretation, CLEO4/CLEO5 runtime and native CLEO plugins are not implemented.

## CLEO inventory

The 25 preserved V8 scripts are:
- GTA V HUD, GTA V Money, GTA V Phone, Aim GTA 5
- Radio Wheel, Weapon Wheel 3, Weapon Wheel Control, Menu Weapons v2
- NVE Gas, R.A.C.S., Realistic Rain, Set Time
- Skin Selector v3, Trans Fender, VehicleSpawn, Color Changer
- Apparel, Cheats, Mission Selector, Passenger
- Quicksave Anywhere, Set Weather, Teleport
- Taillight Headlight Player, Taillight Headlight Time

The two Taillight Headlight scripts are not confirmed to be the same as SA_DOX's documented unsupported “2DFX Taillights” script. Keep them marked for investigation rather than excluding them on a filename guess.

## Future compatible-base recommendation — not for 2.11.311

If a properly supported 32-bit AML build is used separately in the future:
1. Preserve and install all 25 scripts with their required CLEO runtime; do not install the excluded separate 100-CLEO menu.
2. Keep SAUtils, AML Limit Adjuster and JPatch as required by the chosen mods.
3. Use one complete standalone Saadox preset. Ultra is the visual-priority preset; High is the fallback if frame pacing is inadequate.
4. Do not overlay V8's optional SA_DOX folder. The standalone presets share 78/80 files; only Advanced.ini and shaderUniform.ini change between Low, Medium, High and Ultra. Their shared files are the common baseline.
5. Disable the SA_DOX-incompatible V8 AML plugins listed in the SA_DOX Android guide: GPS, MoreSettings, Project2DFX, SAPlanter and SkyGFXMobile.
6. Choose one popcycle.dat. Main and Busy are identical; Low is the lower-traffic alternative.
7. Keep the V8 GTA V Style menu option as a separate, testable texturedb/menu overlay; do not merge its files into unrelated texture databases.
8. Retain original archive paths for IMG, handling, animation and model overrides; compare against a matching clean base game before overwriting.

This future-base checklist does not authorize changing the requested 2.11.311 game installation. No ready-to-install merged package is produced for 2.11.311 because its required native runtime is absent.

## Audit files

- [Full deep-inspection run 6](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/actions/runs/41505606444)
- [Merge plan](../merge-plan/README.md)
- [Archive release](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/releases/tag/v1.0)
