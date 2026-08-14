# user cmake/ — load order

# 1. *_user_option.cmake  — option() / KMCMAKE_* overrides (before deps)
# 2. *_deps.cmake         — find_package / KMCMAKE_DEPS_LINK (if(option))
# 3. *_cxx_config.cmake   — KMCMAKE_CXX_OPTIONS from flags + deps
# 4. *_cpm.cmake          — CPMAddPackage (only when KMCMAKE_USE_CPM)
#
# See docs/AI_MIGRATE.md. CPM.cmake is under kmcmake/tools/; do not copy it here.

you can define things here
