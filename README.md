# libagora-objc

**The Objective-C runtime for Agora** — GNUstep's libobjc2: the runtime that `libagora-base`
(Foundation), `libagora-app` (AppKit) and the rest of the desktop are written against. It provides
`objc_msgSend`, classes, categories, ivars, properties, ARC, blocks, and C++ exceptions/interop.

Agora is an installable desktop environment for Linux and BSD; see the project README in the parent
directory (`../README.md`).

## Provenance

Extracted from GNUstep's [libobjc2](https://github.com/gnustep/libobjc2). See
[`PROVENANCE.md`](PROVENANCE.md); upstream's own README is kept as
[`UPSTREAM-README.md`](UPSTREAM-README.md).

## Licence

MIT, © 2009 David Chisnall and the libobjc2 contributors — see [`COPYING`](COPYING).

## Build

Build into `build/`:

```sh
cmake -S . -B build -DCMAKE_C_COMPILER=clang -DCMAKE_CXX_COMPILER=clang++ \
      -DCMAKE_INSTALL_PREFIX=<prefix>
cmake --build build
cmake --install build
```

The build installs a CMake package, so a consumer uses `find_package(libobjc CONFIG)` with
`-DCMAKE_PREFIX_PATH=<prefix>` — including `libagora-base`.
