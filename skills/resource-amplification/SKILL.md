---
name: resource-amplification
description: >-
  Amplification and parser DoS safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions zip bomb ReDoS XML billion laughs JSON depth length field over-allocation.
---

# Amplification / parser DoS safety

## Threats
Zip/gzip bombs (CWE-409), XML entity expansion (CWE-776), ReDoS (CWE-1333), deep JSON, declared-length over-alloc.

## Methods
- Decompress: max output bytes + ratio + entry count + nest depth; Zip Slip path confine.
- XML: disable DTDs/external entities.
- Regex: linear-time engine (RE2/Go regexp/Rust regex) or length-cap + safe patterns; no `RegExp(user)`.
- Parsers: max depth/keys; compare length fields to remaining input before alloc.
- Images: pixel/byte caps before full decode.

## Checklist
- [ ] Bomb fixtures rejected
- [ ] ReDoS suite or linear engine on user-input regex
- [ ] No DTD expansion on untrusted XML


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
