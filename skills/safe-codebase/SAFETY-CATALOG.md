# Safety catalog (language-agnostic)

English research themes: *memory / thread / type / null safety*, *TOCTOU*, *side-channel / constant-time*, *Unicode safety*, *capability / sandbox / WASM*, *idempotency*, *prompt injection*, *mobile IPC*, *open redirect*, *mass assignment*, *Safe Coding*, *OWASP/CERT*, *supply chain*, *undefined behavior*.

Use every class that applies. For each: **threat → invariant → mitigation pattern**.

## Index (40 classes)

| # | Class | # | Class |
|---|--------|---|--------|
| 1 | Memory | 21 | Unicode / encoding / locale |
| 2 | Thread / concurrency | 22 | Side-channel / constant-time |
| 3 | Type | 23 | TOCTOU / filesystem race |
| 4 | Null / absence | 24 | Capability / sandbox / least privilege |
| 5 | Data / validation | 25 | Plugin / WASM / untrusted code |
| 6 | Injection & encoding | 26 | Idempotency / exactly-once |
| 7 | Integer / arithmetic / bounds | 27 | Randomness / entropy |
| 8 | Resource / availability | 28 | Floating-point / numeric |
| 9 | FFI / unsafe / native | 29 | Cache / HTTP caching |
| 10 | Error / panic | 30 | URL / redirect / deep-link |
| 11 | Cryptographic | 31 | Browser isolation (CORS / postMessage) |
| 12 | Secret & credential | 32 | Mobile IPC / Intent / Binder |
| 13 | Authn / session / authz | 33 | LLM / prompt / agent-tool |
| 14 | Serialization / serde | 34 | State machine / workflow / logic |
| 15 | I/O / path / SSRF | 35 | Schema / migration evolution |
| 16 | Privacy / data protection | 36 | Multi-tenant isolation |
| 17 | Supply-chain & build | 37 | Feature-flag / kill-switch |
| 18 | Time / replay / integrity | 38 | GraphQL / mass-assignment / batch abuse |
| 19 | Configuration / ops | 39 | Realtime / WebSocket / SSE |
| 20 | Observability | 40 | Payment / financial invariants |

---

## 1. Memory safety

**Threats:** buffer overflow/underflow, use-after-free, double-free, dangling pointers, wild reads/writes, uninitialized reads.

**Ensure:**
- Prefer memory-safe languages/modes; confine `unsafe`/manual memory to audited modules.
- Bounds-check all indexing derived from input; never trust length fields without comparing to actual buffer size.
- Clear ownership: who allocates, who frees; no shared mutable aliasing without rules.
- Initialize before read; use Option/`MaybeUninit`-style APIs deliberately.

---

## 2. Thread / concurrency safety

**Threats:** data races, torn reads/writes, deadlocks, livelocks, atomicity violations, TOCTOU.

**Ensure:**
- No unsynchronized shared mutable state across threads.
- Document thread-safety of every public type (immutable / thread-confined / mutex-protected / lock-free with proof).
- Prefer message passing or immutability over shared mutability.
- Make check-then-act sequences atomic at the right layer (locks, DB transactions, compare-and-swap).
- Avoid blocking the UI/critical threads holding locks while calling out.

---

## 3. Type safety

**Threats:** type confusion, invalid bit patterns, unsafe casts, schema drift, `Any`/`Object` bags.

**Ensure:**
- Nominal/domain types for IDs, money, paths (avoid raw `string`/`int` everywhere).
- Explicit conversions; ban unchecked casts in app code.
- Validate external data into typed models at the boundary (parse, don’t validate ad hoc forever).
- Keep generics/templates constrained so invariants hold.

---

## 4. Null / absence safety

**Threats:** null dereference, undefined access, “empty” vs “missing” confusion.

**Ensure:**
- Non-null by default; make absence explicit (`Optional`, `Option`, `?`, `nullable` annotations + checker).
- No silent null returns for error cases — use Result/Either/exceptions consistently.
- Initialize fields; beware partially constructed objects published to other threads.

---

## 5. Data / validation safety

**Threats:** corrupt state, invariant breaks, malicious payloads, business-logic abuse.

**Ensure:**
- Validate **at trust boundaries** (allowlists, ranges, sizes, encodings).
- Enforce domain invariants in constructors/factories.
- Canonicalize before compare (paths, Unicode, URLs).
- Bound collection sizes and string lengths from untrusted input.

---

## 6. Injection & encoding safety

**Threats:** SQL/NoSQL/LDAP/XPath, OS command, HTML/JS XSS, template injection, header/CRLFi, log injection.

**Ensure:**
- Parameterized interfaces / prepared statements — never concatenate untrusted data into code.
- Context-specific output encoding adjacent to the sink (HTML, URL, JS, CSS, shell).
- Prefer safe builders (SQL binders, autoescaping templates) over manual escaping.
- Structured logging; never interpolate raw user input into log formats that become executable.

---

## 7. Integer / arithmetic / bounds safety

**Threats:** overflow/underflow, truncation, wraparound used as length/offset, money rounding bugs.

**Ensure:**
- Checked/saturating arithmetic on attacker-controlled sizes and amounts.
- Validate before `size * count` allocations.
- Explicit signed/unsigned and width conversions.
- Money/quantities: decimal/integer policy fixed and tested.

---

## 8. Resource / availability safety

**Threats:** memory/CPU/FD/thread/connection exhaustion, unbounded queues, zip bombs.

**Ensure:**
- Timeouts on all network and lock waits; cancellation propagation.
- Rate limits, backpressure, bounded channels/queues, load shedding.
- Caps on upload size, compression ratios, recursion depth, regex backtracking (ReDoS).

---

## 9. FFI / unsafe / native boundary safety

**Threats:** unsound wrappers, ABI mismatch, null pointers from C, ownership leaks across languages.

**Ensure:**
- Unsafe/FFI only in designated modules with `SAFETY`/`# Safety` docs.
- Validate lengths, encodings, null-termination **both directions**.
- Safe public wrappers that cannot be misused into UB (Safe Coding).
- Run sanitizers / Miri / ASan on that kernel.

---

## 10. Error / exception / panic safety

**Threats:** broken invariants after partial failure, swallowed errors, info leaks in messages.

**Ensure:**
- Strong exception/panic safety for mutable structures (commit or rollback).
- No empty catches; fail closed for authz/crypto/validation.
- External errors sanitized; internal detail only in protected logs.

---

## 11. Cryptographic safety

**Threats:** weak ciphers/modes, nonce reuse, homemade crypto, predictable RNG, insecure compares.

**Ensure:**
- High-level vetted libraries only; no novel protocols in app code.
- Modern algorithms (AEAD, etc.); secure random for secrets/tokens.
- Constant-time compare for secrets/MAC/password hashes.
- Key separation; never reuse keys across purposes.

---

## 12. Secret & credential safety

**Threats:** secrets in git, images, logs, client bundles; long-lived credentials.

**Ensure:**
- Secret manager / runtime injection; rotate on leak.
- No secrets in source, tests fixtures, or default configs committed.
- Redact at log/trace boundaries; scrub crash dumps.
- Prefer short-lived / workload identity over static passwords.

---

## 13. Authentication, session & authorization safety

**Threats:** broken auth, session fixation/theft, IDOR/BOLA, privilege escalation, CSRF.

**Ensure:**
- Verify identity on every privileged action; regenerate session on privilege change.
- Authorize **resource ownership** (`user.id == resource.owner`) every access.
- Safe cookie flags / token storage; bind tokens to purpose/audience.
- CSRF defenses for cookie-based browser sessions.

---

## 14. Serialization / deserialization safety

**Threats:** insecure deserializers (RCE), prototype pollution, type confusion, schema mismatch.

**Ensure:**
- Allowlist types; avoid polymorphic “gadget” deserializers on untrusted data.
- Schema validation; reject unexpected fields in strict modes where appropriate.
- Versioned contracts between services.

---

## 15. I/O, path, file & SSRF safety

**Threats:** path traversal, symlink escapes, SSRF to metadata/cloud, zip slip.

**Ensure:**
- Resolve and confine paths under an allowlisted root.
- Block link-local/private targets for user-controlled URLs (SSRF).
- Validate file types by content where needed; do not trust client MIME alone.

---

## 16. Privacy / data-protection safety

**Threats:** PII leakage, excessive retention, unsafe client storage, cross-tenant bleed.

**Ensure:**
- Minimize collection; encrypt sensitive data at rest/in transit as required.
- Tenant isolation in queries and caches.
- Clear data-classification for logs and analytics.

---

## 17. Supply-chain & build safety

**Threats:** malicious/vulnerable deps, typo-squatting, compromised CI, unsigned artifacts.

**Ensure:**
- Pin versions; lockfiles; scan CVEs; review new deps.
- Least privilege for CI tokens; signed commits/artifacts where required.
- SBOM / provenance when the project demands it.
- Disable debug backdoors in release builds.

---

## 18. Time, replay & integrity safety

**Threats:** replayed tokens/requests, clock skew bugs, unsigned payloads trusted blindly.

**Ensure:**
- Nonces/TTL on sensitive requests; verify signatures/HMAC before use.
- Monotonic where needed; don’t use manipulable client clocks for authz alone.

---

## 19. Configuration & operational safety

**Threats:** default passwords, open admin, verbose errors in prod, CORS `*`, mis-set TLS.

**Ensure:**
- Secure defaults; fail closed.
- TLS for sensitive channels; HSTS/security headers where web applies.
- Separate prod config; no “god mode” left enabled.

---

## 20. Observability safety

**Threats:** secrets in traces, insufficient audit for security events, log injection.

**Ensure:**
- Audit authn/authz failures and admin actions without logging secrets.
- Structured logs; tamper-evident storage when required.
- Metrics without high-cardinality untrusted strings exploding cardinality / cost.

---

## 21. Unicode / encoding / locale safety

**Threats:** homoglyphs, normalization mismatches, overlong UTF-8, locale-dependent `toLowerCase` auth bugs, Mojibake, bidirectional override spoofing.

**Ensure:**
- Decode once with a strict UTF-8/charset policy; reject illegal sequences.
- Normalize (NFC/NFKC as policy) **before** validate/compare/store.
- Use locale-invariant case folding for security identifiers.
- Beware visually confusable names in allowlists and URLs.

---

## 22. Side-channel / constant-time safety

**Threats:** timing leaks on secrets, cache/memory-access patterns, speculative execution gadgets, power/EM where relevant.

**Ensure:**
- Secret-independent control flow and memory indexes in crypto/auth compares.
- Constant-time compare for passwords, tokens, MACs.
- Prefer vetted crypto libs; don’t hand-roll “almost constant time.”
- Harden against Spectre-class issues in sandboxes that mix secrets and untrusted code.

---

## 23. TOCTOU / filesystem race safety

**Threats:** symlink races, `access`+`open` gaps, temp-file replace, permission check then act on a swapped path.

**Ensure:**
- Prefer `openat`/`openat2` (or OS equivalents) with `O_NOFOLLOW` / resolve-beneath flags; operate on **fds**, not re-resolved path strings.
- Create temps with atomic APIs (`mkstemp`, exclusive create); secure temp dirs.
- Don’t split permission check and use across a mutable namespace.
- Apply same idea to DB/object stores: atomic conditional updates, not check-then-write without versioning.

---

## 24. Capability / sandbox / least-privilege safety

**Threats:** ambient authority, over-broad tokens, container breakout via mounts, “works on my root” privileges in prod.

**Ensure:**
- Default deny; grant minimal FS/net/device capabilities.
- Drop privileges after bind/listen; separate UIDs/roles per component.
- Scope cloud IAM and API tokens to least actions and resources.
- Sandbox parsers and untrusted renderers (seccomp, landlock, AppArmor/SELinux, OS sandboxes).

---

## 25. Plugin / WASM / untrusted-code safety

**Threats:** sandbox escape, host-function overgrant, fuel/memory DoS, non-determinism, malicious hot-reload.

**Ensure:**
- Capability-gated host imports; no ambient WASI FS/net unless required.
- CPU fuel / epoch timeouts and memory ceilings per module.
- Sign/verify plugins; version APIs; treat guest I/O as untrusted.
- Pin time/entropy when determinism is required.

---

## 26. Idempotency / exactly-once / distributed safety

**Threats:** double-charge, duplicate side effects on retry, split-brain writes, lost updates.

**Ensure:**
- Idempotency keys for payments and mutating APIs; dedupe windows.
- At-least-once handlers made idempotent; outbox/inbox patterns where needed.
- Optimistic concurrency (etag/version) or serializable transactions for critical rows.
- Clear retry/backoff policies that don’t amplify damage.

---

## 27. Randomness / entropy safety

**Threats:** `Math.random` / weak PRNG for tokens, seed leakage, deterministic “secrets” in tests shipped to prod.

**Ensure:**
- CSPRNG for security tokens, keys, nonces, password resets (`crypto/rand`, `SecureRandom`, WebCrypto).
- Never use hash(userId) as an auth secret.
- Separate test fakes from production RNG wiring.

---

## 28. Floating-point / numeric safety

**Threats:** money in binary float, NaN/Inf propagation, equality bugs, nondeterministic FP across platforms.

**Ensure:**
- Fixed-point/decimal integers for currency and balances.
- Explicit NaN/Inf handling at boundaries; don’t let NaN pass auth checks.
- Document determinism needs (simulations, consensus); avoid FP in consensus-critical hashes.

---

## 29. Cache / HTTP caching safety

**Threats:** cached private responses, cache poisoning, stale authz, CDN serving wrong tenant.

**Ensure:**
- Correct `Cache-Control` / `Vary` for authenticated content (`private`, no shared cache).
- Include auth/tenant in cache keys; purge on permission changes.
- Validate untrusted headers that influence cache keys (Host, forwarding headers).

---

## 30. URL / redirect / deep-link safety

**Threats:** open redirects, javascript: URLs, intent URI attacks, OAuth redirect_uri mismatch.

**Ensure:**
- Allowlist redirect targets; prefer relative paths under your origin.
- Strict OAuth `redirect_uri` equality; no substring matches.
- Validate deep-link schemes/hosts; don’t auto-open untrusted URLs.
- Sanitize URL display to prevent phishing (punycode/homoglyph UI).

---

## 31. Browser isolation safety (CORS / postMessage / frames)

**Threats:** CORS `*`, trusting `event.origin` poorly, clickjacking, XSS via postMessage.

**Ensure:**
- Explicit CORS origins; no credentials with wildcard.
- Verify `MessageEvent.origin` and shape; don’t `eval` message data.
- Framing controls (`frame-ancestors` / X-Frame-Options) for sensitive UI.
- Separate trusted and untrusted origins (cookie boundaries).

---

## 32. Mobile IPC / Intent / Binder safety

**Threats:** intent hijacking/spoofing, exported components, confused deputy, sticky broadcast abuse.

**Ensure:**
- Export components only when required; permission-guard exported entry points.
- Explicit intents for sensitive actions; verify caller identity/UID where platform allows.
- Validate all extras; never trust implicit broadcast data for authz.
- PendingIntent mutability flags set correctly (Android).

---

## 33. LLM / prompt / agent-tool safety

**Threats:** direct/indirect prompt injection, tool abuse (email/send/pay), data exfil via model context, confused agent actions.

**Ensure:**
- Treat retrieved content as **data**, not instructions (delimit/sanitize untrusted context).
- Allowlist tools; confirm high-impact actions; least-privilege credentials for tools.
- Don’t put secrets in prompts/logs; mediate file/net tools through policy.
- Human-in-the-loop for irreversible operations when product requires it.

---

## 34. State machine / workflow / business-logic safety

**Threats:** skipping payment, replaying steps, illegal status transitions, coupon stacking abuse.

**Ensure:**
- Explicit allowed transitions; enforce server-side, not only UI.
- Single source of truth for order/lifecycle state.
- Abuse-case tests (OWASP ASVS business logic): double submit, step skip, negative qty.
- Feature entitlement checks on every privileged transition.

---

## 35. Schema / migration evolution safety

**Threats:** destructive migrations without lock, dual-write inconsistency, unreadable old rows, expand/contract violations.

**Ensure:**
- Expand/contract migrations; backwards-compatible deploys.
- Migrate with locks/timeouts; avoid long table locks in OLTP without plan.
- Version APIs and persisted documents; readers tolerate prior N versions.
- Backup/restore tested before irreversible schema ops.

---

## 36. Multi-tenant isolation safety

**Threats:** cross-tenant IDOR, shared cache keys, global secondary indexes without tenant prefix, analytics bleed.

**Ensure:**
- Tenant id on every query, cache key, object path, and job payload.
- Defense in depth: row-level security / partitioned schemas when available.
- Test cross-tenant access attempts in CI.
- No “superuser query” paths that omit tenant filters in app code.

---

## 37. Feature-flag / kill-switch safety

**Threats:** flags enabling debug auth, partial rollout of insecure paths, sticky flags left on in prod.

**Ensure:**
- Flags default secure; dangerous flags require multi-party or env gate.
- Kill-switches for abusive features and costly AI/tools.
- Audit flag changes; don’t store secrets in flag payloads.
- Ensure flag evaluation can’t be client-spoofed for security decisions.

---

## 38. GraphQL / mass-assignment / batch-abuse safety

**Threats:** over-fetch, nested query DoS, assigning `role`/`isAdmin` from client body, batch login packing.

**Ensure:**
- Allowlist writable fields; never bind request body straight onto sensitive models.
- Query depth/complexity limits, timeouts, persisted queries where useful.
- Authorize per field/resolver; paginate connections.
- Rate-limit batch and introspection in production as policy dictates.

---

## 39. Realtime / WebSocket / SSE safety

**Threats:** unauthenticated sockets, cross-tenant topics, message injection, resource stickiness DoS.

**Ensure:**
- Authenticate/authorize on connect **and** on subscribe/publish.
- Topic names include tenant; server-side authorize channel access.
- Validate message schemas; backpressure and max message size.
- Idle timeouts; reconnect storm limits.

---

## 40. Payment / financial invariant safety

**Threats:** negative balances, race double-spend, float money, webhook forgery, price tampering from client.

**Ensure:**
- Server-side price/catalog as source of truth; never trust client totals.
- Idempotent payment intents; verify provider webhooks with signatures.
- Ledger-style invariants (debits=credits) where accounting applies.
- Decimal/integer money (#28); transactional balance updates (#26).

---

## Priority heuristic

When everything is on fire, order roughly:

1. Secrets exposed / RCE / injection / authz bypass / payment integrity  
2. Memory UB / data races / TOCTOU in privileged code  
3. LLM tool abuse / mobile exported components / open redirects  
4. Null/type chaos at boundaries / tenant isolation  
5. Resource DoS / side-channels / supply chain / hardening  

Always re-check with [VERIFY.md](VERIFY.md).
