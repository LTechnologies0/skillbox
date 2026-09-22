---
name: resource-downstream-quotas
description: >-
  Downstream quota and fan-out safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions API quota bulkhead fan-out storm vendor rate limit coalesce.
---

# Downstream quota & fan-out safety

## Threats
One tenant burns shared vendor quota; unbounded fan-out per request; retry storms to deps.

## Methods
- Per-tenant budgets and bulkheads for shared deps.
- Cap fan-out (max parallel downstream calls per request).
- Coalesce/cache identical downstream reads.
- Circuit breaker + retry budgets on egress.

## Checklist
- [ ] Fan-out capped
- [ ] Tenant cannot starve others’ quota
- [ ] Egress metrics per dependency


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
