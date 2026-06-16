# Abec — the Abe compiler

**Abec** compiles the [Abe](https://e-tools.ai) programming language (`.abe`) — a
typed, memory-safe systems language with built-in concurrency (ARC + cycle
collector, actors, structured concurrency) — down to LLVM IR and native code.

This repository distributes **prebuilt `abec` binaries and documentation**. Abec is
a **commercial product of E-Tools AI Corporation**; the compiler source is private.
The Abe **runtime** is a separate, freely-linkable library.

> **Status:** `0.1.0` preview · **platform: Linux x86-64** (glibc ≥ 2.34). macOS,
> Windows, and ARM builds are not published yet.

---

## Quick start

```bash
# Download the latest Linux x86-64 build
curl -fsSL -o abec \
  https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/abec-linux-x86_64
chmod +x abec
./abec --version

# Type-check and compile Abe source to LLVM IR
./abec --check    hello.abe
./abec --emit-ll  hello.abe > hello.ll
```

Verify the download against the published checksums (recommended):

```bash
curl -fsSL -O https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/SHA256SUMS
sha256sum -c SHA256SUMS
```

See **[INSTALL.md](INSTALL.md)** for full setup and license activation.

## What this release does

| Command | Works in this release |
|---|---|
| `abec --version`, `--help` | ✅ (no license needed) |
| `abec --check FILE` | ✅ parse + type-check |
| `abec --emit-ll FILE` | ✅ parse + type-check + LLVM IR |
| `abec FILE -o OUT` (native link) | ⚠️ requires the Abe **runtime** — see below |

Producing a **native executable** additionally needs the Abe runtime archives and a
local LLVM toolchain (`llc`/`clang`). The runtime is distributed separately — contact
**licensing@e-tools.ai** for access. (Source-level compilation above needs nothing but
the `abec` binary.)

## Licensing

Abec is licensed software. **Without a license it runs in evaluation mode** — it still
compiles, but prints an `UNLICENSED EVALUATION` banner and stops working after the
build's evaluation window. A license removes the banner and the time limit.

Install a license token any one of these ways:

```bash
# 1. default file
mkdir -p ~/.abe && printf '%s\n' "ABE1.…" > ~/.abe/license
# 2. environment (best for CI / containers)
export ABEC_LICENSE="ABE1.…"
# 3. a file you point to
export ABE_LICENSE_FILE=/path/to/license
```

Precedence: `ABEC_LICENSE` → `ABE_LICENSE_FILE` → `~/.abe/license`. `abec --version`
always works without a license.

**To buy a license or request an extended evaluation: licensing@e-tools.ai.**
Use of Abec is governed by the **[End-User License Agreement](EULA.md)**.

## Example

[**MalariaChat**](https://github.com/E-Tools-AI-Corporation/malariachat) is a full
open-source application written in Abe and built with `abec` — a good place to see the
language in a real codebase.

## Support

- Licensing & sales: **licensing@e-tools.ai**
- Issues with a release (download, checksum, a crash): open an issue in this repo.
- The compiler source is private; bug reports with a minimal `.abe` reproducer are welcome.

© 2026 E-Tools AI Corporation. All rights reserved.
