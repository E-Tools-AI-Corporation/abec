# Changelog

All notable changes to published `abec` releases are documented here.

## v2026.06.16 — 0.1.0 (Linux x86-64)
- Version string is now **0.1.0** (`abec --version`).
- **Native builds now work off-box.** `abec` locates the runtime relocatably
  (`$ABE_RUNTIME_DIR`, then relative to the binary, then system defaults) instead of a
  hardcoded path. New downloads: a **runtime tarball** (`abe-runtime-…tar.gz`, ARRL) and
  a **bundle** (`abec-…tar.gz`, compiler + runtime). See README → Native builds.
- `abec FILE` with **no `-o`** now defaults the output to the input's base name
  (`foo.abe` → `foo`).
- Evaluation grace window is **30 days** per build (was 90); extended trials are
  available as license tokens — contact licensing@e-tools.ai.
- Same compiler capabilities as the preview (`--check`, `--emit-ll`; ARC + cycle
  collector; structured concurrency, actors, Sendable checks; offline Ed25519
  licensing). Self-contained (libc only), glibc ≥ 2.34.

## v2026.06.15 — 0.1.0 preview (Linux x86-64)
- First public release.
- Parse, type-check, and LLVM IR emission for Abe v4.1 source
  (`abec --check`, `abec --emit-ll`).
- Memory model: ARC + Bacon–Rajan cycle collector. Concurrency: structured
  concurrency, actors, compile-time Sendable checks.
- Offline license enforcement (Ed25519): evaluation mode with a built-in window;
  a license key removes the banner and time limit.
- Self-contained binary (links only libc), glibc ≥ 2.34.

Native linking (`abec FILE -o OUT`) requires the separately-distributed Abe runtime.
