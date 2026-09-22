# Verification gates for “safe”

A codebase is not “safe” because someone said so. Attach evidence.

## Baseline scenario

Record: language, build mode, OS, commands run, before/after for each fix class.

## Universal checks

| Gate | What |
|------|------|
| Build / typecheck | Strict mode on (null/type flags enabled) |
| Unit/integration tests | Boundary rejection cases + concurrency tests where relevant |
| Secret scan | gitleaks/trufflehog/org scanner — clean |
| Dependency scan | known CVEs addressed or waived with reason |
| Lint / SAST | project security rules green or triage documented |

## Memory / UB / races (native & unsafe)

| Tool family | Use |
|-------------|-----|
| ASan / UBSan / MSan | C/C++ and FFI |
| TSan | data races |
| Miri | Rust unsafe |
| Valgrind | where applicable |
| Go `-race` | Go concurrency |
| ThreadSanitizer / race kits | others |

## Appsec / data

| Method | Use |
|--------|-----|
| SAST | injection, insecure APIs |
| DAST / manual route check | runtime authz/IDOR / open redirect |
| Fuzz parsers | untrusted binary/text formats |
| Property/invariant tests | domain constructors, money/ledger |
| Idempotency tests | retry same key → one side effect |
| Tenant isolation tests | cross-tenant ID access must 403 |

## Extra gates by class

| Class | Extra check |
|-------|-------------|
| #22 Side-channel | secret-dependent branch audit; constant-time compare tests |
| #23 TOCTOU | fd-based file ops; no access+open pairs |
| #25 WASM/plugins | capability list review; fuel/timeout tests |
| #27 RNG | grep for weak PRNG on security paths |
| #30 Redirects | allowlist unit tests; OAuth redirect_uri exact match |
| #32 Mobile IPC | exported component audit; intent fuzz |
| #33 LLM/agents | injection cases in retrieved docs; tool allowlist review |
| #38 GraphQL | depth/complexity limits proven in config |
| #40 Payments | webhook signature tests; client price tamper rejected |

## Config / privacy

- No secrets in repo tree or sample env committed with real values.
- Prod logging redaction spot-check.
- TLS/cookie/header review for web.
- Cache-Control on authenticated responses (#29).


## Definition of done (per engagement)

- [ ] Every applicable catalog class reviewed (N/A justified)
- [ ] Critical/High findings fixed or explicitly accepted by user
- [ ] Unsafe/FFI modules documented with safety contracts
- [ ] Verification commands listed and run (or blocked with environment reason)
- [ ] No new secrets introduced; leaked secrets rotation called out

Do not mark the whole codebase “entirely safe” — mark **classes hardened** with evidence.
