# kmcmake Project Status

Last updated: 2026-07-25

## Current Release State

| Item | Value |
|------|--------|
| Template version (in-tree) | **1.6.0** (`TM_KMCMAKE_VERSION` / README) |
| Latest published git tag | `v1.5.1` |
| 1.6.0 release | **Not tagged yet** — changes are local (xxd, CPM, clang-format, `c_cpp_project_rules.md`, …) |
| Install (last published) | `kmdo install kmcmake@v1.5.1` |

## Delivery Pipeline Status

Verified workflow (unchanged):

1. tag (`vX.Y.Z`)
2. release (GitHub/GitLab)
3. mirror publish
4. mirror pull/sync
5. install
6. use

Notes:

- `kmdo` enforces clean git state before mirror operations
- If local mirror workspace gets conflicted/dirty, deleting the local mirror copy and re-pulling is an effective recovery path

## Lineage (recent)

| Version | Highlights |
|---------|------------|
| **1.6.0** (WIP) | `kmcmake_cc_xxd`; vendored CPM.cmake **v0.43.1** + `KMCMAKE_USE_CPM`; default `.clang-format`; `docs/c_cpp_project_rules.md` |
| **1.5.1** | Ignore `build-ninja` |
| **1.5.0** | Layered template baseline; `LINKS`/`PLINKS` → OBJECT; x-ci `@v2`; `AI_UPGRADE_1_5.md` |
| **1.4.x** | `kmcmake/` vs `cmake/` split; `AI.md` / `skills.h`; per-library `SHARE`; arch SIMD |

## Capability Snapshot (1.6.0 tree)

- Target macros: library / interface / object / binary / test / benchmark / proto / proto_object / **xxd**
- Dep channels (user-explicit, no auto-detect): system `find_package`, kmpkg/vcpkg toolchain, optional **CPM**
- AI surface: `docs/AI.md`, `docs/c_cpp_project_rules.md`, `docs/AI_UPGRADE*.md`, `<project>/skills.h`
- Style: root `.clang-format` (WebKit-based, aligned with xhash)

## Validation Status

- Historical: `test/run_template_regression.sh` passed on 1.5.x (SIMD `NONE` / `AVX2` / `AVX512`, external consumer)
- 1.6.0 local smoke (not a full regression re-run):
  - Template generate + install
  - `kmcmake_cc_xxd` + `base_xxd_test` passed
  - `KMCMAKE_USE_CPM` OFF/ON configure (CPM include-only, no fetch) OK
- **Before tagging 1.6.0:** re-run `bash test/run_template_regression.sh` on a clean tree

## Documentation Status

| Doc | Role |
|-----|------|
| `template/docs/AI.md` | AI constraints, layout, `kmcmake_cc_*` API |
| `template/docs/c_cpp_project_rules.md` | C/C++ naming, includes, deps/cxx ownership |
| `template/docs/AI_UPGRADE.md` | Pre-v1 → layered |
| `template/docs/AI_UPGRADE_1_5.md` | → 1.5.0+ ops |
| `README.md` / `README_CN.md` | User entry; version **1.6.0** |
| `CHANGELOG.md` / `CHANGELOG_CN.md` | Release notes |

Quick-start (unchanged):

```bash
kmdo kmpkg gencmake -n your-project-name -o your-path
# or
cmake -S ./template -B build-template -DCHANGEME=myproject
cmake --install build-template --prefix /path/to/myproject
```

## Known Operational Notes

- `test/.work/` and `test/.work_*/` are gitignored regression/smoke artifacts
- Generated xxd outputs land under the source tree for IDE use; ignore via `*.xxd.h` / `*.xxd.cc` / `xxd_gen.*` (configure only **hints**, does not edit `.gitignore`)
- CPM is opt-in (`KMCMAKE_USE_CPM`); overlapping with kmpkg/vcpkg is the user's responsibility

## Suggested Next Steps

1. Commit + tag **v1.6.0** after full regression
2. Optional: kmpkg `depend-info` → `*_cpm.cmake` exporter / AI guide (planned, not started)
3. Align template demo snippets with `c_cpp_project_rules.md` where they still diverge (e.g. quote includes in generated xxd aggregate)
4. Keep CI / docs version strings in sync on each tag
