---
name: resource-connections
description: >-
  Connection pool resource safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions database connection pool HTTP client pool gRPC leak checkout timeout.
---

# Connection pool safety

## Threats
DB/HTTP/gRPC/Redis pool exhaustion; leaks; wait forever on checkout.

## Methods
- Set max open/idle; **checkout wait timeout**.
- One request should not checkout unbounded connections (N+1).
- Circuit break sick dependencies.
- Propagate cancel to queries (`QueryContext`, etc.).

## Checklist
- [ ] Pool max < DB max_connections / replicas sharing
- [ ] Checkout timeout set
- [ ] Slow query/dependency cannot pin all connections indefinitely


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
