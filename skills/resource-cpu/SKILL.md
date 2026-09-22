---
name: resource-cpu
description: >-
  CPU / compute resource safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions CPU exhaustion password hashing image processing sync loops worker offload.
---

# CPU / compute resource safety

## Threats
CPU DoS via expensive endpoints (search, export, argon2/bcrypt floods), tight loops, sync image/video work on request threads.

## Methods
- Move CPU-heavy work to **bounded** worker pools.
- Cost-based rate limits on expensive routes; auth before heavy work.
- Cap iterations/recursion; reject pathological inputs early.
- Don’t run unbounded sync compute on the UI/event-loop thread.

## Checklist
- [ ] Expensive ops queued with concurrency cap
- [ ] Login/hash endpoints rate-limited
- [ ] No O(input²) surprises on public parsers without caps


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
