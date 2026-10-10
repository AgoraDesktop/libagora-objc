# Provenance

This repository is an extraction of GNUstep's libobjc2 — the Objective-C runtime — at a pinned
revision. It is the runtime that Foundation and the rest of Agora's Objective-C code is compiled and
linked against.

| | |
|---|---|
| Upstream repository | https://github.com/gnustep/libobjc2 |
| Upstream revision | `v2.3` |
| Upstream commit | `e877e782fb965c4e870d2ecf6aff58dddc6290ae` |
| Upstream commit date | 2025-09-16 |
| Extraction | the upstream repository root, placed at this repository's root |
| Extraction date | 2026-10-10 |
| Source changes by Agora | none |

## What was copied

The whole upstream tree at the pinned revision (197 files) — the runtime sources (`runtime.c`,
`class_table.c`, `selector_table.cc`, `dtable.c`, `ivar.c`, `objc_msgSend.*.S`, `arc.mm`,
`blocks_runtime.m`, …), the public headers under `objc/` plus `Block.h` / `blocks_runtime.h`, and
`Test/`, `CMake/`, `CMakeLists.txt`, `Config.cmake.in`, `libobjc.pc.in`, `COPYING`, `ANNOUNCE*`,
`API` and `ABIDoc`.

Dropped: only VCS and CI metadata — `.git/`, `.github/`, `.gitignore`, `.gitmodules`, `.mailmap`.
Agora supplies its own `.gitignore`, and upstream's `README.md` is kept as `UPSTREAM-README.md`
so this repository can carry its own `README.md`.

Upstream already installs a CMake package (`install(EXPORT libobjcTargets)`,
`configure_package_config_file(Config.cmake.in …)` → `libobjcConfig.cmake`, plus `libobjc.pc`), so
`find_package(libobjc CONFIG)` works without changes.

## Why this revision

`v2.3` is the revision the Foundation in `libagora-base` was written and built against — the Fiwix
tree pins this same tag and produces `libobjc.so.4.6`. Pinning the same revision keeps the imported
Foundation valid.

## Licence and attribution

MIT, © 2009 David Chisnall and the libobjc2 contributors. Upstream's `COPYING` is retained verbatim
and the per-file headers are untouched. Agora's own additions to this repository fall under the
project licence (see the top-level project README).

## Keeping in sync

Updates re-extract from a newer upstream revision and record it in the table above. Because the tree
sits at the repository root, `git diff` against a fresh upstream checkout shows local divergence
directly.
