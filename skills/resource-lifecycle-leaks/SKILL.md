---
name: resource-lifecycle-leaks
description: >-
  Resource lifecycle and leak safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions resource leak finally defer close ownership lost reference.
---

# Lifecycle & leak safety

## Threats
Lost references before release; unclear ownership; error paths skip close (CWE-400 scenarios).

## Methods
- Single owner; `defer`/`finally`/`with`/`using` always.
- Prefer scoped guards / RAII / `Closing` wrappers.
- On cancel/error: release checkouts, delete temps, abort streams.
- Soak tests for FD/memory/goroutine leaks.

## Checklist
- [ ] Error-path review for missing close
- [ ] Leak soak green
- [ ] Ownership documented for pooled resources


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
