# 更新日志

## 2026-08-15 — v1.7.0

### 文档
- `AI_MIGRATE.md`：普通 CMake → kmcmake（`user_option` → `deps` → `cxx_config`）。
- `AI_UPGRADE.md`：已有 kmcmake → 1.7 检查表（`/tmp` 骨架、替换 `kmcmake/`）。
- `VCPKG.md`：标准 overlay/registry port，只用 `vcpkg_cmake_configure` / `install` / `config_fixup`。
- `AI_UPGRADE_1_5.md` 改为指向 `AI_UPGRADE.md` 的短页。

## 2026-07-25 — v1.6.0

### 新功能
- 新增 `kmcmake_cc_xxd`（及 `kmcmake_xxd_script.cmake`）：将静态资源嵌入为 `std::string_view` C++ 源码，并提供汇总接口 `xxd_gen_files()`。
- 模板示例嵌入 `assets/hello.txt`，并附带 `base_xxd_test` 校验生成结果。
- 模板 `.gitignore` 增加 `*.xxd.h` / `*.xxd.cc` / `xxd_gen.*`；configure 仅打印 STATUS 提示，不主动改 `.gitignore`。
- 在 `kmcmake/tools/CPM.cmake` 内化官方 **CPM.cmake v0.43.1**（未修改）。
- 新增 `KMCMAKE_USE_CPM`（默认 OFF）与模板 `cmake/*_cpm.cmake`；开启后 `*_deps.cmake` 在 `find_package` 之前 include（不做 kmpkg/vcpkg 自动探测）。
- 模板 CPM 示例拉取 **fmt 10.2.1**；开启 CPM 时跑 `base_cpm_test`。
- CI 增加 `cpm+xxd`（ubuntu/mac、空工具链）：`-DKMCMAKE_USE_CPM=ON`，校验 `xxd_gen` 产物并跑 `base_xxd_test` + `base_cpm_test`。
- `.gitattributes`：`assets/**` 标记 `-text`，避免 Windows CRLF 破坏嵌入字节；`base_xxd_test` 同时接受 LF/CRLF。
- 默认附带 `.clang-format`（与 xhash 一致，基于 WebKit 风格），供 IDE / `clang-format` 使用。
- 新增 `docs/c_cpp_project_rules.md`：生成工程的 C/C++ 源码、目录、依赖与 AI 修改约定。
- `CMakePresets.json`：默认 Ninja（`build/`）；Unix Makefiles 用 `-make`（`build-make/`）。三条依赖线（空 / CPM / vcpkg）× `default`/`test`/`test-bm`/`all`。不再注入 `KMPKG_CMAKE`。
- 默认生成只装 `${PROJECT}::skills`（`skills.h` + `version.h`）。教学例子（foo / xxd / tests / examples）需 `-DKMCMAKE_GEN_EXAMPLES=ON`。生成入口 `vckgctl gencmake`（kmdo 已废弃）。

## 2026-07-21 — v1.5.1

### Bug 修复
- 模版 `.gitignore`：忽略 `build-ninja`（ninja preset 输出目录）。

## 2026-07-21 — v1.5.0

1.4 分层模版（`kmcmake/` 框架 vs `cmake/` 用户配置）的稳定基线。

### Bug 修复
- `kmcmake_cc_library`：将 `LINKS` / `PLINKS` 转发到内部 `*_OBJECT` 编译单元，使依赖的 usage requirements（头文件路径、宏定义）在编译 `SOURCES` 时生效。

### 文档
- 新增 `template/docs/AI_UPGRADE_1_5.md` — 面向 AI 的操作型升级指南（在 `/tmp` 生成骨架、替换 `kmcmake/`、拷贝 `CMakePresets.json`）。

### CI
- 主 CI 从 x-ci `@v1`（`ci-template.yml`）迁移到平台级 `@v2`（`ubuntu` / `alpine` / `centos` / `mac` / `windows`）。
- 移除本地 `windows.yml`；Windows MSVC + Ninja 由 x-ci v2 `windows.yml` 覆盖。

## 2026-07-18 — v1.4.1 (lothar)

### 模版重构
- 框架层（`kmcmake/`）与用户配置层（`cmake/`）分离——模版升级不再覆盖用户设置。
- 创建 `template/docs/AI.md`，包含约束、工作流、API 参考、skills.h 规范、变量表以及常见修改场景。
- 创建 `template/docs/AI_UPGRADE.md`，提供从旧结构迁移到新结构的分步指南。
- 创建 `template/changeme/skills.h.in` — AI 可读的 Doxygen 风格项目摘要。
- 新增 `@CHANGEME_LOW@` 模版变量，用于小写项目名。

### 动态库 — 按库选择加入
- 移除全局 `KMCMAKE_ENABLE_SHARE` 选项。
- `kmcmake_cc_library` 新增 `SHARE` 布尔选项——仅在设置时构建/安装动态库。
- 动态库输出名称改为 `<name>_shared`（如 `libfoo_shared.so`），避免与静态库在 Windows 上导入库冲突。
- 仅当设置 `SHARE` 时创建 `<namespace>::<name>` 别名。

### Bug 修复
- 修复 `kmcmake_module.cmake` 中 `elseif()` → `else()` 错误。
- 修复 macOS ARM64 构建：NEON/VFPv4/FMA 标志仅在 32 位 ARM 下启用；AppleClang ARM64 randen 标志置空。
- 修复 `KMCMAKE_DISTRO_VERSION_ID` 变量名。
- `USER_CXX_FLAGS` 重命名为 `KMCMAKE_CXX_OPTIONS`。
- 修复 MSVC ARM 架构标志：MSVC 上 `-march` / `-mfpu` 标志置空（不支持）。
- 修复 MSVC ARM randen 标志：MSVC 上排除 `-march=armv8-a+crypto` / `-mfpu=neon`。
- 修复 x86 SSE4 标志：移除多余的尾部分号，避免产生空字符串项。

### CI
- 创建 `.github/workflows/ci.yml`，矩阵包含：ubuntu-latest、ubuntu-22.04、ubuntu-24.04-arm、macos-latest、windows-latest、centos:stream9。
- 在根目录 `README.md`、`README_CN.md` 及 `template/README.md` 添加 CI 徽章（`?branch=master`）。
- 禁用故意失败的演示测试（在 `tests/CMakeLists.txt` 中用注释块包围）。
