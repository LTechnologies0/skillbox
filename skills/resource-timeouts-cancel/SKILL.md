---
name: resource-timeouts-cancel
description: >-
  Timeouts cancellation and shutdown safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions timeout deadline cancellation AbortSignal context shutdown drain slowloris.
---

# Timeouts, cancellation & shutdown

## Threats
Slowloris; forever waits; abandoned work continues; shutdown hangs.

## Methods
- Server: read header/body/write/idle timeouts.
- Client: deadline on every outbound call; inherit parent context.
- Propagate cancel; cleanup in finally; honor cancellation in loops.
- Shutdown: stop intake → drain with deadline → force cancel → release.

## Checklist
- [ ] No outbound call without timeout policy
- [ ] Cancel stops downstream work
- [ ] Shutdown completes within declared deadline under load


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
