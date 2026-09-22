---
name: resource-disk
description: >-
  Disk and storage resource safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions disk fill upload quota temp files inode log retention.
---

# Disk / storage resource safety

## Threats
Disk/inode fill via uploads, logs, temps, extracts.

## Methods
- Cap upload size and total per-user storage.
- Secure temps (`mkstemp`); delete on completion/failure.
- Log rotation + retention; separate volumes for high-churn data.
- Archive extract: entry count, total uncompressed, path confinement.

## Checklist
- [ ] Upload limits enforced
- [ ] Temp cleanup on error
- [ ] Logs rotated; disk alerts configured


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
