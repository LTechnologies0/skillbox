# Language & stack mapping

Map each safety class to **native** mechanisms. Prefer what the project already uses; do not invent a second stack.

## Memory

| Stack | Prefer |
|-------|--------|
| Rust | Safe Rust; confine `unsafe`; Miri/ASan |
| C/C++ | Bounds libs, smart pointers, ASan/UBSan; avoid raw `new[]` soup |
| Java/Kotlin/C#/Go/Python/JS | GC languages still need bounds on buffers/NIO/FFI |
| Swift | ARC; avoid unsafe pointers without audit |

## Threads

| Stack | Prefer |
|-------|--------|
| Rust | `Send`/`Sync`; channels; `Mutex`/`RwLock` |
| Java/Kotlin | `synchronized` / concurrent collections / structured concurrency |
| Go | Don’t share memory; share by communicating; race detector |
| C/C++ | atomics + documented locking; TSan |
| JS | Workers + structured clone; no shared mutable without Atomics/SAB discipline |
| Python | GIL ≠ safety; use queues; care with C extensions |

## Null / absence

| Stack | Prefer |
|-------|--------|
| Kotlin/Swift/Rust/TypeScript strict | Non-null types / `Option` |
| Java | JSpecify / NullAway / Optional at boundaries |
| C# | nullable reference types enabled |
| Python | `Optional` + type checker (Pyright/mypy) |
| Go | pointers explicit; don’t overuse `nil` interfaces |

## Types & data at boundaries

- Parse JSON/XML/protobuf into validated structs.
- Use schema libraries already in the repo.
- Reject unknown critical fields when protocol is strict.

## Injection

- SQL: bound parameters / query builders.
- Shell: argv arrays, never `system(userString)`.
- HTML: autoescaping templates / sanitized markdown allowlists.
- XSS: encode by context; CSP where web.

## Crypto & secrets

- Use platform crypto (libsodium, WebCrypto, Java Cryptography, .NET crypto) — not rolled ciphers.
- Secret stores: Vault, cloud SM, OS keychain — match ops environment.

## FFI

- JNI / cgo / PyO3 / napi / cxx — treat as C threat model.
- Validate every cross-boundary value; document ownership.

## Web / API extras

- Authn/z middleware on every route; IDOR checks on IDs.
- CSRF for cookie sessions; SameSite; secure cookies.
- SSRF allowlists for egress.
- Redirect allowlists; CORS explicit origins; postMessage origin checks.
- GraphQL: depth/complexity limits; mass-assignment allowlists.

## Mobile

- Minimize exported components; explicit intents; validate extras.
- Correct PendingIntent mutability; protect deep links.

## Plugins / WASM / agents

- Capability-gated host functions; fuel + memory limits.
- Delimit untrusted retrieved content for LLMs; allowlist tools.

## Money / distributed

- Decimal/integer money; idempotency keys; webhook signature verify.

If the language is unknown: apply [SAFETY-CATALOG.md](SAFETY-CATALOG.md) logically and ask the user which tools their build already runs.
