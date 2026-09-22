---
name: resource-fd-handles
description: >-
  File descriptor and handle safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions file descriptor exhaustion socket leak ulimit handle close.
---

# FD / handle resource safety

## Threats
FD exhaustion from connection/file leaks or accept storms.

## Methods
- Size pools so `max_connections * processes < soft ulimit` with headroom.
- Always close/detach on error and cancel paths (`defer`/`finally`/`using`).
- Prefer pooled clients over per-request `open`.
- Monitor FD usage; alert before ulimit.

## Checklist
- [ ] Soak test: FD count stable
- [ ] All file/socket opens paired with close
- [ ] Accept/listen backlog and limits understood


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
