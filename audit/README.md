# Audit records

## Completed source archive inspection

- [Compatibility matrix for game build 2.11.311](compatibility-matrix.md)
- [Full merge plan and decisions](../merge-plan/README.md)
- [GitHub Actions deep-inspection run 6](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/actions/runs/41505606444)
- [Source archive release v1.0](https://github.com/jazplaysmcpe2-cloud/gta-sa-mod-audit/releases/tag/v1.0)

## Verified scan summary

| Check | Result |
|---|---:|
| Files indexed | 771 |
| Total uncompressed bytes | 3,083,717,961 |
| Nested archives found / extracted | 5 / 5 |
| Native shared-library entries | 20 |
| ELF architecture | All 20 are 32-bit ARM |
| CLEO scripts | 25 (12 .csa, 13 .csi) |
| GTA IMG archives parsed | 9 |
| IMG directory entries | 17,157 |
| Duplicate names inside IMG archives | 0 |
| ZIP archives inspected | 6 |
| Duplicate ZIP member paths | 0 |
| Normalized target path collision groups | 81 |
| Same-content duplicate groups | 55 |
| Different-content alternatives | 26 |

The 26 different-content path alternatives are accounted for: 25 SA_DOX paths distinguish V8's optional graphics folder from standalone Saadox presets, and one is the alternative `data/popcycle.dat` traffic file. No unexplained path conflict remains in the extracted files.

## Runtime decision

Do not install the supplied AML / CLEO / SAUtils / JPatch / SA_DOX native stack into the requested GTA SA Android 2.11.311 build: it is an arm64-v8a process, while every native .so in the uploaded archives is ELF32 / ARM. The supplied libraries cannot load into that process. All CLEO script files are preserved in the audit inventory, but their supplied runtime is blocked.

Full game installation and in-game performance testing have not been performed. The IMG parsing is structural; a clean matching 2.11.311 base archive was not available for original-versus-modified binary comparison.
