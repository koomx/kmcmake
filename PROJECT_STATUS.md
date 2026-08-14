# kmcmake Project Status

Last updated: 2026-08-15

## Current Release State

| Item | Value |
|------|--------|
| Template version (in-tree) | **1.7.0** (`TM_KMCMAKE_VERSION` / README) |
| Latest published git tag | `v1.5.1` |
| 1.7.0 release | **Not tagged yet** |

## Lineage (recent)

| Version | Highlights |
|---------|------------|
| **1.7.0** (WIP) | Presets: empty / CPM / vcpkg × Ninja+make; `KMCMAKE_GEN_EXAMPLES`; default `${PROJECT}::skills`; `AI_MIGRATE` / `AI_UPGRADE` / `VCPKG` docs |
| **1.6.0** (untagged) | `kmcmake_cc_xxd`; CPM.cmake v0.43.1; `.clang-format`; `c_cpp_project_rules.md` |
| **1.5.1** | Ignore `build-ninja` |
| **1.5.0** | Layered template; `LINKS`/`PLINKS` → OBJECT; x-ci `@v2` |
| **1.4.x** | `kmcmake/` vs `cmake/` split; `skills.h`; arch SIMD |

## Capability Snapshot (1.7.0 tree)

- Target macros: library / interface / object / binary / test / benchmark / proto / proto_object / xxd
- Dep channels: system `find_package`, vcpkg toolchain, optional CPM (no auto-detect)
- Config pipeline: `user_option` → `deps` → `cxx_config`
- Default generate: `skills` INTERFACE only; teaching demos behind `KMCMAKE_GEN_EXAMPLES`
- Docs: `AI.md`, `AI_MIGRATE.md`, `AI_UPGRADE.md`, `VCPKG.md`, `c_cpp_project_rules.md`

## Documentation Status

| Doc | Role |
|-----|------|
| `template/docs/AI.md` | AI constraints, layout, `kmcmake_cc_*` |
| `template/docs/AI_MIGRATE.md` | Plain CMake → kmcmake |
| `template/docs/AI_UPGRADE.md` | Existing kmcmake → 1.7 |
| `template/docs/VCPKG.md` | Standard vcpkg port |
| `template/docs/c_cpp_project_rules.md` | C/C++ + cmake ownership |
| `README.md` / `README_CN.md` | User entry; version **1.7.0** |

Quick-start:

```bash
vckgctl gencmake -n your-project-name -o your-path
# or
cmake -S ./template -B build-template -DCHANGEME=myproject
cmake --install build-template --prefix /path/to/myproject
```

## Suggested Next Steps

1. Tag **v1.7.0** after `bash test/run_template_regression.sh`
2. Keep CI / docs version strings in sync on each tag
