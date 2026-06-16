# Installing Abec

Platform: **Linux x86-64**, glibc ≥ 2.34. The binary links only the C standard
library — no other runtime dependencies for source-level compilation.

## 1. Download

```bash
curl -fsSL -o abec \
  https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/abec-linux-x86_64
curl -fsSL -O \
  https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/SHA256SUMS
sha256sum -c SHA256SUMS        # expect: abec-linux-x86_64: OK
chmod +x abec
```

Optionally place it on your `PATH`:

```bash
sudo install -m 0755 abec /usr/local/bin/abec
abec --version
```

## 2. Activate your license

Without a license, `abec` runs in **evaluation mode** (compiles, with an
`UNLICENSED EVALUATION` banner, until the build's evaluation window ends). To run
license-clean, install your token (from licensing@e-tools.ai) one of these ways:

```bash
# Option A — default file (simplest for a workstation)
mkdir -p ~/.abe
printf '%s\n' "ABE1.YOUR-TOKEN" > ~/.abe/license
chmod 600 ~/.abe/license

# Option B — environment variable (best for CI / containers)
export ABEC_LICENSE="ABE1.YOUR-TOKEN"

# Option C — point to a file
export ABE_LICENSE_FILE=/secure/path/abec.license
```

If more than one is present, precedence is `ABEC_LICENSE` → `ABE_LICENSE_FILE` →
`~/.abe/license`.

Verify activation — this should print **no** banner:

```bash
printf 'function main(): i64 { return 0; }\n' > /tmp/hello.abe
abec --emit-ll /tmp/hello.abe > /dev/null    # silent == licensed
```

`abec --version` always works, with or without a license.

## 3. Compile Abe source

```bash
abec --check   yourfile.abe          # parse + type-check (errors/warnings)
abec --emit-ll yourfile.abe > out.ll # parse + type-check + LLVM IR
```

## 4. Native executables (with the Abe runtime)

`abec yourfile.abe -o yourprog` (or just `abec yourfile.abe` → `./yourfile`) needs the
Abe **runtime** plus a local LLVM toolchain (`clang` + `llc` on `PATH`).

Easiest is the **bundle** (compiler + runtime together):

```bash
curl -fsSL -O https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/abec-0.1.0-linux-x86_64.tar.gz
tar xzf abec-0.1.0-linux-x86_64.tar.gz && cd abec-0.1.0
bin/abec hello.abe && ./hello        # abec finds runtime/ automatically
```

Or install the **runtime separately** (freely redistributable, ARRL) next to an existing
`abec`, or point at it:

```bash
curl -fsSL -O https://github.com/E-Tools-AI-Corporation/abec/releases/latest/download/abe-runtime-0.1.0-linux-x86_64.tar.gz
tar xzf abe-runtime-0.1.0-linux-x86_64.tar.gz       # -> runtime/
export ABE_RUNTIME_DIR="$PWD/runtime"               # or place runtime/ beside the abec binary
```

`abec` resolves the runtime via `$ABE_RUNTIME_DIR` → `<bin>/../runtime` →
`<bin>/../lib/abe/runtime` → `<bin>/runtime` → `/usr/local/lib/abe/runtime`. Omitting
`-o` defaults the output to the input's base name (`foo.abe` → `foo`). Programs using
postgres/redis/http/crypto also need the matching **system** libraries installed
(`libpq`, `libhiredis`, `libcurl`, `libssl`).

## Troubleshooting

| Symptom | Cause / fix |
|---|---|
| `UNLICENSED EVALUATION` banner | No token found, or wrong path. Check `ABEC_LICENSE` / `ABE_LICENSE_FILE` / `~/.abe/license`; remove stray whitespace. |
| `… expired on YYYY-MM-DD` | Subscription lapsed — contact licensing@e-tools.ai to renew. |
| `… malformed or its signature is invalid` | Token truncated/edited — re-copy it exactly. |
| `evaluation period ended … blocked` | Unlicensed past the build's eval window — install a license or download a newer build. |
| `cannot execute binary file` | Wrong architecture/OS — this build is Linux x86-64 only. |
