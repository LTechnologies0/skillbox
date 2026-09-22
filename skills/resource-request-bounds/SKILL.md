---
name: resource-request-bounds
description: >-
  HTTP and API request bound safety. Methods to prevent exhaustion and DoS for this resource class. Use with resource-safety umbrella or when the user mentions max body size headers URL multipart GraphQL complexity batch limit.
---

# Request / API surface bounds

## Threats
Huge bodies/headers/URLs; multipart storms; GraphQL alias/batch/complexity bombs.

## Methods
- Max body (streaming), header count/size, URL length, multipart parts/size.
- GraphQL: depth + complexity limits; care with batching; lock down introspection in prod if policy says so.
- Cap page sizes and list limits on all list endpoints.

## Checklist
- [ ] Proxy and app agree on body limits
- [ ] GraphQL complexity enforced
- [ ] Pagination max page size enforced server-side


## Related

- Parent umbrella: skill `resource-safety` (`skills/resource-safety/` — DOMAINS.md, METHODS.md, VERIFY.md).
- Sister domains: memory, cpu, fd-handles, concurrency, connections, disk, queues-backpressure, timeouts-cancel, rate-limit-shed, amplification, request-bounds, lifecycle-leaks, downstream-quotas.
