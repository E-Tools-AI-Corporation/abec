# Changelog

All notable changes to published `abec` releases are documented here.

## v1.0.0 — 1.0.0 (Linux x86-64)

First stable release of the Abe Pro Compiler.

- **Version bump 0.2.0 → 1.0.0** (`abec --version`).
- **Codegen correctness — string comparison fix:** `arr[i] == arr[j]` where both
  operands are `string[]` element loads now correctly lowers to
  `abe_string_equals` (dual-rep tolerant) instead of a raw pointer compare, which
  returned `false` even for equal strings. A regression test guards it.
- **ARC memory-management hardening:** push-grown containers, string-concat and
  `+=`/reassignment temporaries, and borrow-only sinks (e.g. `console.log`) are
  released correctly; the Abe-program leak harness passes its 0-leak assertions.
- **Cross-module symbol de-duplication** for co-compiled modules (no duplicate
  class-method / helper emission).
- Carries the relocatable runtime resolution (`ABE_RUNTIME_DIR` → binary-relative
  → system paths), default output name (`abec FILE` → `FILE` basename), and the
  offline Ed25519 license gate with an evaluation grace period.
- **Documentation:** the previously-separate Level 1 and Level 2 manuals are now
  a single **[Abe Pro Programming Manual](https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/ABE-Pro-Programming-Manual-1.0.0.md)**
  release asset — Part I (Level 1, general applications, 14 chapters), Part II
  (Level 2, machine learning, 22 chapters), and a runtime-function reference.
  Every example is still compiled and run by the regression suite. The two
  levels also remain available as standalone assets — the
  **[Abe Pro User Manual (Level 1)](https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/ABE-Pro-User-Manual-1.0.0.md)**
  and the **[Abe Pro ML Manual (Level 2)](https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/ABE-Pro-ML-Manual-1.0.0.md)** (both version-bumped 0.2.0 → 1.0.0 to track the compiler release).
- **Integrity:** `SHA256SUMS` now covers the manual assets as well as the
  binaries, so `sha256sum -c SHA256SUMS` verifies every published asset.
- **PDF editions:** the three user manuals are also published as PDFs under
  [`pdf/`](pdf/) — [Programming Manual](pdf/ABE-Pro-Programming-Manual-1.0.0.pdf),
  [User Manual (Level 1)](pdf/ABE-Pro-User-Manual-1.0.0.pdf), and
  [ML Manual (Level 2)](pdf/ABE-Pro-ML-Manual-1.0.0.pdf) — same content as the
  Markdown assets.

## v2026.06.21 — 0.2.0 (Linux x86-64)
- **Version bump 0.1.0 → 0.2.0** (`abec --version`) — the Level-2 machine-learning
  surface is a feature addition over the 0.1.x line.
- **Complete ML toolchain**, all compiling to native code with no Python or
  external tensor library:
  - **Quantization** — Q8_0 / Q4_0 weight quantization, dequant-on-the-fly
    matmul, compact save/load, GGUF interop.
  - **Mixed precision** — bf16/f16 compact 16-bit storage and a bf16-numeric
    `@amp` training forward.
  - **GPU backend** — optional CUDA/cuBLAS backend, op-for-op parity with the
    CPU reference (`abec --gpu`).
  - **Autograd as a first-class API** — `gradient`, `einsum`, `jacobian`,
    `valueAndGrad` (with positional `let (v, g) = …` destructuring).
  - **Convolution** — `conv2d` / `conv1d`.
  - **Object-oriented layers & models** — custom methods, `init()` bodies, model
    `config {}`, `noGrad` / `inferenceMode`.
  - **Weight tying** — `shared param` and cross-submodule tying.
  - **Mixture of Experts** — `router` gates, `xavier_uniform`, `topk_gate`,
    `moe_balance_loss`.
  - **Custom gradients** — a layer `backward(gradOut): gradIn`.
  - **Knowledge distillation** — `distill student on data from teacher { … }`.
  - **Conversational AI** — `prompt` / `ask` / `respond` / `confirm`.
  - **Distributed training** — `@ddp(worldSize: N)` single-process
    gradient-accumulation DDP; `@fsdp`/`@tensorParallel`/`@pipeline` acknowledged
    with an honest diagnostic.
  - **ONNX interoperability** — `onnx_load` / `onnx_run` / `onnx_free` via
    onnxruntime (optional build dependency; default binary links an honest
    "not available" stub).
- **Abe Pro ML Manual (Level 2)** expanded to **22 chapters**
  (`ABE-Pro-ML-Manual-0.2.0.md`); every example compiled and run by the
  regression suite.

## v2026.06.18 — 0.1.0 (Linux x86-64)
- **New: the Abe Pro Machine Learning Manual (Level 2)** —
  `ABE-Pro-ML-Manual-0.1.0.md`. Nine chapters (Tensors, Layers, Models,
  Attention/transformer blocks, Inference & generation, Tokenization, Serving,
  Loading models, Training); every example compiled and run by the regression
  suite. Covers the Llama-class stack through KV-cache generation, real
  safetensors weights, serving, and training + LoRA.
- Compiler (runtime byte-for-byte unchanged): tensor × scalar arithmetic infers
  a tensor; a wildcard × scalar stays a wildcard; typed `let x: T = …` parses
  inside `ai { }` blocks.

## v2026.06.17 — 0.1.0 (Linux x86-64)
- **Array methods completed:** `.reduce`, `.find`, `.includes` / `.indexOf`,
  `.slice` / `.pop` result typing (joining `.map` / `.filter` / `.forEach`).
- **Regular expressions:** `regexTest`, `regexMatch`, `regexReplace` built-ins.
- **Database:** parameterized-query result iteration compiles —
  `dbResultRowCount` / `dbResultColCount` infer `i64`.
- **Grammar spec erratum:** bare scalar annotations (`: i64`, `: f64`, `: bool`)
  now expressed in `abe-v4.1.g4` (`abec` always accepted them).
- Compiler-side only; the runtime is byte-for-byte unchanged from `v2026.06.16`.

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
