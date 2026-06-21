# Abec — the Abe compiler

**Abec** compiles the [Abe](https://e-tools.ai) programming language (`.abe`) — a
typed, memory-safe systems language with built-in concurrency (ARC + cycle
collector, actors, structured concurrency) — down to LLVM IR and native code.

This repository distributes **prebuilt `abec` binaries and documentation**. Abec is
a **commercial product of E-Tools AI Corporation**; the compiler source is private.
The Abe **runtime** is a separate, freely-linkable library.

> **Status:** `0.2.0` · **platform: Linux x86-64** (glibc ≥ 2.34). macOS,
> Windows, and ARM builds are not published yet.

> ### 🆕 What's new in 0.2.0 (`v2026.06.21`)
>
> **A complete machine-learning toolchain — no Python, no framework.** Abec now
> compiles a full Llama-class ML stack straight to native code: tensors, neural
> `layer`s / `model`s, transformer blocks (RoPE, RMSNorm, SwiGLU, grouped-query
> attention), KV-cache inference + `generate`, tokenization, serving, real
> safetensors/GGUF loading, and `train` (AdamW/SGD, autograd, LoRA).
>
> New in this release: **quantization** (Q8_0/Q4_0), **mixed precision**
> (bf16/f16 + `@amp`), an optional **CUDA GPU backend**, a first-class **autograd
> API** (`gradient`, `einsum`, `jacobian`, `valueAndGrad`), **convolution**,
> **object-oriented layers/models** (custom methods, `init()`, `config`),
> **weight tying**, **Mixture of Experts**, **custom gradients**, **knowledge
> distillation**, **conversational-AI** statements, single-process **distributed
> training** (`@ddp`), and **ONNX** interoperability.
>
> The **[Abe Pro ML Manual (Level 2)](https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/ABE-Pro-ML-Manual-0.2.0.md)**
> documents all of it across **22 chapters** — every example compiled *and run*
> by the regression suite. See the full [release notes](https://github.com/E-Tools-AI-Corporation/abec/releases/tag/v2026.06.21)
> and the [CHANGELOG](CHANGELOG.md).

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
| `abec FILE -o OUT` (native link) | ✅ with the Abe **runtime** — see [Native builds](#native-builds) |

Source-level commands need only the `abec` binary. **Native executables** also need the
Abe **runtime** and a local LLVM toolchain (`clang` + `llc`).

## Native builds

Two ways to get a working compiler + runtime:

**A. Bundle (simplest)** — one download with `abec` + the runtime side by side:

```bash
curl -fsSL -O https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/abec-0.2.0-linux-x86_64.tar.gz
tar xzf abec-0.2.0-linux-x86_64.tar.gz && cd abec-0.2.0
bin/abec hello.abe        # -> ./hello   (abec finds runtime/ automatically)
./hello
```

**B. Separate runtime** — if you already have `abec`. The runtime is freely
redistributable under the [ARRL](ABE-RUNTIME-REDISTRIBUTION-LICENSE.md):

```bash
curl -fsSL -O https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/abe-runtime-0.2.0-linux-x86_64.tar.gz
tar xzf abe-runtime-0.2.0-linux-x86_64.tar.gz     # -> runtime/
export ABE_RUNTIME_DIR="$PWD/runtime"             # or place runtime/ next to the abec binary
abec hello.abe
```

`abec` locates the runtime via `$ABE_RUNTIME_DIR`, then relative to its own binary
(`<bin>/../runtime`, `<bin>/../lib/abe/runtime`, `<bin>/runtime`), then
`/usr/local/lib/abe/runtime`. Requires `clang` + `llc` on `PATH`. If `-o` is omitted the
output name defaults to the input's base name (`foo.abe` → `foo`). Programs that use
postgres/redis/http/crypto also need the matching system libraries (`libpq`, `hiredis`,
`libcurl`, `libssl`) installed.

## Documentation

Two manuals ship with every release as downloadable assets. Every example in
both is compiled **and run** by the regression suite, so the code you read is
code that works.

- **[Abe Pro User Manual (Level 1)](https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/ABE-Pro-User-Manual-0.2.0.md)**
  — general-purpose programming and the runtime library.
- **[Abe Pro ML Manual (Level 2)](https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/ABE-Pro-ML-Manual-0.2.0.md)**
  — the machine-learning toolchain (22 chapters): tensors, layers/models,
  attention, inference + `generate`, tokenization, serving, loading weights,
  training + LoRA, quantization, mixed precision, GPU, autograd/einsum,
  convolution, OO layers/models, weight tying, Mixture of Experts, custom
  gradients, distillation, conversational AI, distributed training, and ONNX.

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
always works without a license. License validation is **offline** — no internet
connectivity is required for licensed operation.

**You own what you build.** A compiler license does not encumber your output: you keep
all rights to your code, you can sell software built with Abec, and the **Abe™ Runtime is
royalty-free to deploy and redistribute** with your applications (static *or* dynamic
linking) — no per-user, per-device, or per-install fees.

**To buy a license or request an extended evaluation: licensing@e-tools.ai.**

License documents (the PDFs in [`legal/`](legal/) are the controlling versions):
- **[Abec™ Compiler EULA](EULA.md)** — terms for using the compiler.
- **[Abe™ Runtime Redistribution License (ARRL)](ABE-RUNTIME-REDISTRIBUTION-LICENSE.md)** — royalty-free runtime redistribution.
- **[Licensing FAQ](LICENSING-FAQ.md)** — the short answers.

## Example

[**MalariaChat**](https://github.com/E-Tools-AI-Corporation/malariachat) is a full
open-source application written in Abe and built with `abec` — a good place to see the
language in a real codebase.

## Support

- Licensing & sales: **licensing@e-tools.ai**
- Issues with a release (download, checksum, a crash): open an issue in this repo.
- The compiler source is private; bug reports with a minimal `.abe` reproducer are welcome.

© 2026 E-Tools AI Corporation. All rights reserved.
