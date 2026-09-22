---
name: resource-safety
description: >-
  Hardens any-language systems against uncontrolled resource consumption
  (CWE-400/770): memory, CPU, FDs, threads/workers, connections, disk, queues,
  timeouts/cancellation, rate limits, load shedding, amplification DoS
  (zip/ReDoS/XML), request bounds, lifecycle leaks, downstream quotas. Use when
  the user says resource safety, DoS hardening, backpressure, pool exhaustion,
  rate limit, zip bomb, ReDoS, or bounded concurrency.
---

# Resource Safety (umbrella)

## Goal

Every scarce resource a cheap request can consume has an **explicit cap**, a **full-policy** (block / reject / shed), **timeouts + cancellation**, and **release on all paths**. Prefer fail-fast (429/503) over unbounded buffering (delayed OOM).

## Domain skills (install alongside this one)

| Skill name | Focus |
|------------|--------|
| `resource-memory` | Heap/RSS budgets, alloc caps, streaming |
| `resource-cpu` | CPU bombs, costly hashing, parse CPU |
| `resource-fd-handles` | FDs, sockets, file handles |
| `resource-concurrency` | Threads, workers, async tasks, semaphores |
| `resource-connections` | DB/HTTP/gRPC pools |
| `resource-disk` | Disk, inodes, temp files, uploads |
| `resource-queues-backpressure` | Bounded queues, backpressure |
| `resource-timeouts-cancel` | Deadlines, cancel propagation, shutdown |
| `resource-rate-limit-shed` | Rate limits, load shedding, retry storms |
| `resource-amplification` | Zip/XML/JSON bombs, ReDoS, nested parse |
| `resource-request-bounds` | Body/header/URL/multipart/GraphQL limits |
| `resource-lifecycle-leaks` | Close/return resources; no lost refs |
| `resource-downstream-quotas` | Third-party API/quota amplification |

Catalogs: [DOMAINS.md](DOMAINS.md) · [METHODS.md](METHODS.md) · [VERIFY.md](VERIFY.md)

## Agent protocol

1. Inventory resources the attack surface can touch.
2. For each: max size, max count, timeout, owner who releases, saturation behavior.
3. Fix **unbounded → bounded** first; then timeouts; then rate limits; then amplification.
4. Prefer the matching domain skill for deep fixes; use this umbrella for full audits.
5. Verify with load + bomb fixtures ([VERIFY.md](VERIFY.md)).

## Hard rules

- Unbounded queue/channel/buffer fed by the network is a defect.
- Missing outbound timeout is a defect (turns slow deps into pool death).
- Retries without backoff/jitter/budget amplify outages.
- Caps must apply while **streaming**, not after buffering entire bodies.
