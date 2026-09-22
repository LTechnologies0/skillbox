---
name: resource-rate-limit-shed
description: >-
  Rate limiting and load shedding safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions rate limit token bucket load shedding 429 503 retry storm backoff.
---

# Rate limiting & load shedding

## Threats
Floods; expensive endpoint abuse; retry storms amplifying outages.

## Methods
- Per-principal limits primary; per-IP secondary (IPv6 /64).
- Weight costly endpoints higher.
- On saturation: shed with 429/503 + Retry-After; **don’t** buffer unboundedly.
- Client/server retries: budget, exponential backoff, jitter; idempotent only.

## Checklist
- [ ] Limits on unauthenticated and authenticated paths
- [ ] Shed metrics visible
- [ ] Retries cannot amplify without bound


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
