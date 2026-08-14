# kmcmake

[中文版](README_CN.md)

[![CI](https://github.com/koomx/kmcmake/actions/workflows/ci.yml/badge.svg?branch=master)](https://github.com/koomx/kmcmake/actions/workflows/ci.yml)

[Website / docs](https://pub.kumose.cc/kmcmake/) · [Changelog](CHANGELOG.md)

**Current template version: 1.7.0**

## What is kmcmake?

**kmcmake** is a CMake project template and thin build framework for C/C++ libraries and tools in the [kumo](https://github.com/kumose) ecosystem.

You generate a project once from `template/`. Each generated repo has two layers:

| Layer | Path | Who owns it |
|-------|------|-------------|
| Framework | `kmcmake/` | Replaced on upgrade — do not hand-edit |
| User config | `cmake/` | Your deps, options, flags aggregation |
| Product code | `<project>/`, `tests/`, … | Your sources |

Day-to-day you declare targets with helpers such as `kmcmake_cc_library`, `kmcmake_cc_binary`, `kmcmake_cc_test`. Pick a preset channel: empty (`default` / `test` / …), CPM (`cpm` / `cpm-test` / …), or vcpkg (`vcpkg` / `vcpkg-test` / …, needs `VCPKG_ROOT`). Names without `-make` use Ninja (`build/`); `-make` uses Unix Makefiles (`build-make/`). Edit `cmake/*_cpm.cmake` for CPM packages (vendored [CPM.cmake](https://github.com/cpm-cmake/CPM.cmake) under `kmcmake/tools/`).

## Features

- Layered template: upgrade-safe `kmcmake/` vs editable `cmake/`
- Target macros: library (static + optional `SHARE`), object, interface, binary, test, benchmark, protobuf
- Presets: Ninja default (`build/`); Makefile twins with `-make` (`build-make/`). Channels: empty / CPM / vcpkg × `default`/`test`/`test-bm`/`all`
- Per-arch SIMD detection (x86 / ARM) → `KMCMAKE_CXX_OPTIONS` and generated `version.h` macros
- Packaging hooks (CPack / deb / rpm) and optional Python packaging scripts
- CI via [x-ci](https://github.com/koomx/x-ci) `@v2` (Linux glibc/musl/CentOS, macOS, Windows MSVC)
- AI-oriented docs under `template/docs/` (constraints, API, upgrade steps)

## Template docs (`template/docs/`)

| Doc | Purpose |
|-----|---------|
| [AI.md](template/docs/AI.md) | AI context: constraints, layout, API |
| [AI_MIGRATE.md](template/docs/AI_MIGRATE.md) | Plain CMake → kmcmake (`user_option` / `deps` / `cxx_config`) |
| [AI_UPGRADE.md](template/docs/AI_UPGRADE.md) | Existing kmcmake → **1.7.0** (checklist) |
| [VCPKG.md](template/docs/VCPKG.md) | Standard vcpkg/vckgctl port; worked example [`vcpkg.json`](template/docs/vcpkg.json) + [`portfile.cmake`](template/docs/portfile.cmake) |
| [c_cpp_project_rules.md](template/docs/c_cpp_project_rules.md) | C/C++ source and project rules |

### For AI agents

**Before changing this repo or upgrading a consumer project, read the docs above yourself** (do not guess from memory):

1. Always start with [`template/docs/AI.md`](template/docs/AI.md) and [`c_cpp_project_rules.md`](template/docs/c_cpp_project_rules.md).
2. New project from plain CMake → [`template/docs/AI_MIGRATE.md`](template/docs/AI_MIGRATE.md).
3. Upgrading an existing kmcmake project → [`template/docs/AI_UPGRADE.md`](template/docs/AI_UPGRADE.md).
4. Publishing as a vcpkg port → [`template/docs/VCPKG.md`](template/docs/VCPKG.md).

These files are installed into generated projects as `docs/` when you run `cmake --install` from `template/`.

## Requirements

- CMake **≥ 3.31** to *generate* from this repo’s `template/`
- Generated projects: CMake **≥ 3.31**, C++17+ toolchain (GCC / Clang / AppleClang / MSVC)
- Optional: [vckgctl](https://github.com/koomx) (`gencmake`), Ninja

## Quick start

### Generate a project from the template

```bash
cmake -S ./template -B build-template -DCHANGEME=myproject
cmake --install build-template --prefix /path/to/myproject
# teaching demos (foo / xxd / tests): add -DKMCMAKE_GEN_EXAMPLES=ON
```

### Or via vckgctl

```bash
vckgctl gencmake -n myproject -o /path/to/myproject
```

### Build a generated project

```bash
cd /path/to/myproject
cmake --preset=default          # Ninja, empty deps, option defaults
# cmake --preset=test           # Ninja + tests
# cmake --preset=cpm-test       # CPM + tests
# cmake --preset=vcpkg-all      # vcpkg + tests/benchmark/examples (needs VCPKG_ROOT)
# cmake --preset=default-make   # Unix Makefiles → build-make/
cmake --build build --parallel
ctest --test-dir build --output-on-failure
```

Edit user config under `cmake/` (`*_deps.cmake`, `*_user_option.cmake`, …). Declare libraries in `<project>/CMakeLists.txt` with `kmcmake_cc_*`. Use `SHARE` on a library when you need a shared variant.

## Generated layout (simplified)

```
myproject/
├── CMakeLists.txt          # Entry: kmcmake_module + cmake/*
├── CMakePresets.json       # empty / cpm / vcpkg × Ninja + -make
├── docs/                   # AI.md, c_cpp_project_rules.md, upgrade guides
├── kmcmake/                # Framework — replace on upgrade
│   ├── kmcmake_module.cmake
│   ├── kmcmake_option.cmake
│   ├── arch/               # x86 / arm SIMD
│   └── tools/              # cc_library, cc_test, cc_xxd, ar_*, …
├── cmake/                  # User config — edit freely
│   ├── myproject_deps.cmake
│   ├── myproject_cxx_config.cmake
│   └── …
├── myproject/              # Sources (+ skills.h, version.h.in)
├── tests/
├── examples/
└── benchmark/
```

## Upgrading an existing project

Do **not** overwrite `cmake/` or product sources. Generate a fresh skeleton under `/tmp`, then replace `kmcmake/` and copy `CMakePresets.json`. Checklist: [AI_UPGRADE.md](template/docs/AI_UPGRADE.md). Plain CMake import: [AI_MIGRATE.md](template/docs/AI_MIGRATE.md).

## License

This repository is under the [Apache License 2.0](LICENSE).

Generated projects ship an Apache-2.0 `LICENSE` by default; you may replace it. The kmcmake project license does **not** bind the copyright or license choice of code you generate from the template — that remains yours.
