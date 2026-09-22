---
name: safe-codebase
description: >-
  Makes any-language codebase comprehensively "safe" across 40 safety classes:
  memory, thread, type, null, data, injection, overflow, resources, FFI, errors,
  crypto, secrets, authz, serde, SSRF, privacy, supply chain, TOCTOU, side-channels,
  Unicode, sandbox/WASM, idempotency, LLM/prompt injection, mobile IPC, tenant
  isolation, payments, and more. Use when the user says make safe, harden,
  memory/thread/null/type safety, secure the codebase, or eliminate UB and races.
---

# Make a Codebase Safe (any language)

## Goal

Drive the codebase toward being **entirely safe** in the engineering sense:

- **Safe by construction** where the language/tooling allows (types, ownership, checked APIs).
- **Safe by encapsulation** where risk must exist (FFI, crypto, parsers): small audited kernels + safe wrappers.
- **Safe at boundaries** for untrusted input, concurrency, I/O, plugins, LLMs, and supply chain.
- **Fail secure** — errors deny by default; no silent corruption or secret leakage.

This skill is language-agnostic. Map each safety class to the user’s stack ([LANGUAGE-MAP.md](LANGUAGE-MAP.md)). Full taxonomy (**40 classes**): [SAFETY-CATALOG.md](SAFETY-CATALOG.md).

## Non-negotiable order

```text
1. Understand project + trust boundaries
2. Inventory unsafe surfaces (see catalog)
3. Risk-rank findings (exploitability × blast radius)
4. Propose hardening plan → wait for approval if large
5. Fix by safety class (safe abstractions first)
6. Verify with tests, sanitizers, linters, threat checks
```

Do **not** claim “entirely safe” without evidence. Prefer: measurable reduction of unsafe surfaces + green verification gates.

---

## Agent protocol

1. **Map trust boundaries** — what is untrusted (user input, network, files, plugins, model context, deps)? What is privileged?
2. **Scan every safety class** in [SAFETY-CATALOG.md](SAFETY-CATALOG.md) that applies to this stack (skip only with explicit “N/A + why”).
3. **Prefer Safe Coding pattern** (Google-style): remove risky ops from app code; encapsulate in safe modules whose APIs cannot be misused into UB/vulns; let the typechecker/linter enforce the rest.
4. **Preserve behavior** users rely on unless they asked for security-breaking changes (e.g. rejecting bad input is OK and expected).
5. **Document safety contracts** on unsafe/FFI/crypto/sandbox modules (`SAFETY:`, `# Safety`, preconditions).
6. **Verify** with stack-native tools ([VERIFY.md](VERIFY.md)).

## Hard rules

- No invented vulnerabilities or files — cite path/evidence.
- No “just use a memory-safe language rewrite” as the only answer unless the user asked for a rewrite; harden **this** codebase.
- Do not disable safety checks to “make it compile.”
- Do not log secrets, tokens, passwords, or raw PII.
- Do not introduce `unsafe`/raw pointers/FFI without a documented invariant and a safe wrapper.
- Secrets never committed; fix leaks by rotating, not only deleting from git history advice.
- Treat LLM/tool and plugin boundaries as untrusted by default (#25, #33).

## Output shape (audits)

```markdown
### Safety audit
**Scope:** …
**Trust boundaries:** …
**Findings** (by class 1–40): … evidence … severity …
**Plan:** … safe abstractions / boundary checks …
**Verification:** … tools + expected green checks …
```

When implementing: group PRs/commits by safety class when possible (e.g. null safety, then injection, then threads).
