# GTA SA Mod Audit

Workspace for auditing two user-supplied GTA San Andreas Android mod archives:

- GTA V Project Modpack V8
- Saabox / Saadox 2.0 DirectX 3.0 Graphics Style

## Target setup

- Game version: GTA San Andreas Android 2.11.311
- Device target: POCO F6
- Priorities: GTA V-style HUD and presentation; high-fidelity graphics; retain CLEO and SAUtils.
- Exclusion: the separate 100-CLEO menu previously rejected by the user.

## Upload the archives

Please attach the original ZIP archives to a GitHub Release named `v1.0`:

1. Open **Releases** → **Create a new release**.
2. Tag: `v1.0`
3. Title: `GTA SA Mod Archives for Audit`
4. Attach both original ZIP files as release assets.
5. Publish the release and share its URL.

Do not put large ZIPs in the repository's normal Git files.

## Audit status

No binary archive has yet been extracted or verified in this repository. The TXT inventory previously shared is a path listing only; it does not establish file contents, hashes, plugin ABI, or compatibility.

After the archives are available, the audit will record:

- archive contents and duplicate paths
- CLEO scripts and plugin dependencies
- graphics assets and configuration overlaps
- conflicting files and merge decisions
- installation order and staged test checklist

## Redistribution

Only upload files you have permission to redistribute. If redistribution is not permitted, keep the repository private or use a permitted private transfer method.
