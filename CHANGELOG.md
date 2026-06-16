# Changelog

All notable changes to published `abec` releases are documented here.

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
