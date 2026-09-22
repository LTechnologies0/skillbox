---
name: resource-queues-backpressure
description: >-
  Queue bounds and backpressure safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions bounded queue backpressure channel buffer load pipeline saturation.
---

# Queues & backpressure safety

## Threats
Unbounded queues convert latency problems into OOM; pressure never reaches the edge.

## Methods
- Every queue/channel has **max depth** and **full-policy**.
- Propagate backpressure upstream (pull/demand, pause, or reject).
- Producers check depth before bulk enqueue.
- Prefer shed at edge over infinite buffer mid-pipeline.

## Checklist
- [ ] No unbounded `Queue()` / unbuffered-fed-unbounded growth patterns on network input
- [ ] Metrics: depth, reject/full counts
- [ ] Load test: depth plateaus under overload


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
