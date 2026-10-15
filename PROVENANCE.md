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
| Source changes by Agora | **one file**: `arc.mm` — `object_getRetainCount_np` on a small object. See below. |

## Agora's changes

**One, and it is a crash fix in `arc.mm`.**

`object_getRetainCount_np` read the reference count out of the word BEFORE the object without first
settling the cases where there is no such word. Every other door that reads that word asks first:
`retain()` and `release()` go through `isPersistentObject()`, which answers YES for nil, for a SMALL
OBJECT and for an instance of a class marked permanent. The small-object case is the one that bites,
because a small object is a TAGGED POINTER — there is no `isa` to read at all, and reading offset 0 of
one dereferences the tag bits as an address.

MEASURED: `[@"a string literal" retainCount]` SEGFAULTED while `[@"a string literal" retain]`,
`… release]`, `… autorelease]`, `… length]` and `… UTF8String]` were all fine — every one of those
settles the small-object case first, and this door was the only one that went straight for the header.
The literal is a tagged pointer whose class is `NSTinyString`.

The fix asks `isPersistentObject()` and then the fast-ARC flag, exactly as `retain()` and `release()`
do, and answers the SATURATED count (`refcount_max`) when neither applies. That is what "this object is
never released" means, and it is Apple's own reading of `retainCount` for a constant string: a very
large number rather than a real count.

It is held by `libagora-base/tests/bridges.m`, in the `reg_tagged` case. That suite is where the two
libraries are tested together, and that case had ALREADY recorded this same finding for the bridge's
own door — "Reading offset 0 of one dereferences 0xd19766cde000002c" — so the runtime's door belongs
beside it. Upstream's own suite under `Test/` would be the other home for it and is NOT wired into
this workspace's test run: it sits behind the `TESTS` CMake option, off by default.

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
