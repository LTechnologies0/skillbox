---
name: resource-memory
description: >-
  Memory resource safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions heap RSS buffer cache allocation budgets streaming parsers OOM.
---

# Memory resource safety

## Threats
Large allocations from length fields, unbounded buffer growth, cache without eviction, holding full uploads in RAM.

## Methods
- Stream request/response bodies; enforce max bytes **while** reading.
- Cap caches (entries + TTL + optional max bytes); measure RSS.
- Validate `size*count` with overflow checks before alloc.
- Prefer arena/frame allocators for ephemeral UI/parse temps where applicable.
- Reject absurd Content-Length / chunk totals early.

## Checklist
- [ ] Max body/upload enforced in streaming layer
- [ ] No unbounded `bytes.Buffer` / string concat on untrusted input
- [ ] Caches have max size + eviction
- [ ] Image/decode paths have pixel/byte caps


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
