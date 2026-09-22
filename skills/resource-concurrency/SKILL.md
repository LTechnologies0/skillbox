---
name: resource-concurrency
description: >-
  Concurrency and worker pool safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions thread pool goroutine leak semaphore bounded concurrency spawn limit.
---

# Concurrency / worker pool safety

## Threats
Unbounded thread/goroutine/task spawn; pool starvation; lock held across slow IO.

## Methods
- Fixed-size pools or semaphores for in-flight work.
- Separate pools (bulkheads) for dissimilar workloads.
- Never spawn unlimited tasks per request without a cap.
- Don’t hold locks across await/network.

## Checklist
- [ ] Max in-flight documented and enforced
- [ ] Saturation returns shed/reject, not silent queue forever
- [ ] Goroutine/thread count bounded under load test


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
