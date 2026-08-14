# kmcmake

[English](README.md)

[![CI](https://github.com/koomx/kmcmake/actions/workflows/ci.yml/badge.svg?branch=master)](https://github.com/koomx/kmcmake/actions/workflows/ci.yml)

[网站 / 文档](https://pub.kumose.cc/kmcmake/) · [更新日志](CHANGELOG_CN.md)

**当前模板版本：1.7.0**

## 项目是什么？

**kmcmake** 是面向 [kumo](https://github.com/kumose) 生态的 C/C++ **项目模板 + 轻量构建框架**。

从 `template/` 生成一次工程后，仓库分成两层：

| 层级 | 路径 | 谁维护 |
|------|------|--------|
| 框架 | `kmcmake/` | 升级时整树替换 — 不要手改 |
| 用户配置 | `cmake/` | 依赖、选项、编译选项聚合 |
| 业务代码 | `<project>/`、`tests/` … | 你的源码 |

日常用 `kmcmake_cc_library`、`kmcmake_cc_binary`、`kmcmake_cc_test` 声明目标。Preset 三条线：空（`default` / `test` / …）、CPM（`cpm` / `cpm-test` / …）、vcpkg（`vcpkg` / `vcpkg-test` / …，需 `VCPKG_ROOT`）。无 `-make` 后缀为 Ninja（`build/`）；`-make` 为 Unix Makefiles（`build-make/`）。CPM 包写在 `cmake/*_cpm.cmake`（官方 [CPM.cmake](https://github.com/cpm-cmake/CPM.cmake) 已内化在 `kmcmake/tools/`）。

## 能力概览

- 分层模板：可升级的 `kmcmake/` vs 可改的 `cmake/`
- 目标宏：库（静态 + 可选 `SHARE`）、OBJECT、INTERFACE、可执行文件、测试、benchmark、protobuf
- Preset：默认 Ninja（`build/`）；Makefile 对应名加 `-make`（`build-make/`）。三条线：空 / CPM / vcpkg × `default`/`test`/`test-bm`/`all`
- 分架构 SIMD 探测（x86 / ARM）→ `KMCMAKE_CXX_OPTIONS` 与生成的 `version.h` 宏
- 打包钩子（CPack / deb / rpm）及可选 Python 打包脚本
- CI 使用 [x-ci](https://github.com/koomx/x-ci) `@v2`（Linux glibc/musl/CentOS、macOS、Windows MSVC）
- `template/docs/` 提供面向 AI 的约束、API 与升级步骤

## 模板文档（`template/docs/`）

| 文档 | 用途 |
|------|------|
| [AI.md](template/docs/AI.md) | AI 上下文：约束、目录、API |
| [AI_MIGRATE.md](template/docs/AI_MIGRATE.md) | 普通 CMake → kmcmake（`user_option` / `deps` / `cxx_config`） |
| [AI_UPGRADE.md](template/docs/AI_UPGRADE.md) | 已有 kmcmake → **1.7.0** 检查表 |
| [VCPKG.md](template/docs/VCPKG.md) | 标准 vcpkg/vckgctl port；对照例子 [`vcpkg.json`](template/docs/vcpkg.json) + [`portfile.cmake`](template/docs/portfile.cmake) |
| [c_cpp_project_rules.md](template/docs/c_cpp_project_rules.md) | C/C++ 源码与工程约定 |

### 给 AI Agent

**改本仓库或升级下游工程前，请自行阅读上表文档**（不要凭记忆猜测）：

1. 一律先读 [`template/docs/AI.md`](template/docs/AI.md) 与 [`c_cpp_project_rules.md`](template/docs/c_cpp_project_rules.md)。
2. 普通 CMake 迁过来 → [`template/docs/AI_MIGRATE.md`](template/docs/AI_MIGRATE.md)。
3. 升级已有 kmcmake 工程 → [`template/docs/AI_UPGRADE.md`](template/docs/AI_UPGRADE.md)。
4. 做成 vcpkg port → [`template/docs/VCPKG.md`](template/docs/VCPKG.md)。

从 `template/` 执行 `cmake --install` 时，这些文件会安装到生成项目的 `docs/` 目录。

## 环境要求

- **生成**本仓库模板：CMake **≥ 3.31**
- **生成后的工程**：CMake **≥ 3.31**，C++17+（GCC / Clang / AppleClang / MSVC）
- 可选：[vckgctl](https://github.com/koomx)（`gencmake`）、Ninja

## 快速开始

### 从模板生成工程

```bash
cmake -S ./template -B build-template -DCHANGEME=myproject
cmake --install build-template --prefix /path/to/myproject
# 教学例子（foo / xxd / tests）：加 -DKMCMAKE_GEN_EXAMPLES=ON
```

### 或使用 vckgctl

```bash
vckgctl gencmake -n myproject -o /path/to/myproject
```

### 构建生成后的工程

```bash
cd /path/to/myproject
cmake --preset=default          # Ninja，空依赖，option 默认值
# cmake --preset=test
# cmake --preset=cpm-test
# cmake --preset=vcpkg-all      # 需 VCPKG_ROOT
# cmake --preset=default-make   # Unix Makefiles → build-make/
cmake --build build --parallel
ctest --test-dir build --output-on-failure
```

用户配置改 `cmake/`（`*_deps.cmake`、`*_user_option.cmake` 等）。在 `<project>/CMakeLists.txt` 用 `kmcmake_cc_*` 声明目标；需要动态库时在对应库上加 `SHARE`。

## 生成后的目录（简图）

```
myproject/
├── CMakeLists.txt          # 入口：kmcmake_module + cmake/*
├── CMakePresets.json       # 空 / cpm / vcpkg × Ninja + -make
├── docs/                   # AI.md、c_cpp_project_rules.md、升级指南
├── kmcmake/                # 框架 — 升级时整树替换
│   ├── kmcmake_module.cmake
│   ├── kmcmake_option.cmake
│   ├── arch/               # x86 / arm SIMD
│   └── tools/              # cc_library、cc_test、cc_xxd、ar_* …
├── cmake/                  # 用户配置 — 可自由修改
│   ├── myproject_deps.cmake
│   ├── myproject_cxx_config.cmake
│   └── …
├── myproject/              # 源码（含 skills.h、version.h.in）
├── tests/
├── examples/
└── benchmark/
```

## 升级已有工程

不要覆盖 `cmake/` 或业务源码。在 `/tmp` 生成新骨架，再替换 `kmcmake/` 并拷贝 `CMakePresets.json`。检查表：[AI_UPGRADE.md](template/docs/AI_UPGRADE.md)。普通 CMake 迁入：[AI_MIGRATE.md](template/docs/AI_MIGRATE.md)。

## 许可证

本仓库采用 [Apache License 2.0](LICENSE)。

生成工程默认带 Apache-2.0 的 `LICENSE`，可自行替换。kmcmake 项目许可证**不约束**你从模板生成代码的版权与许可证选择——由你自行决定。
