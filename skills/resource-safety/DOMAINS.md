# Resource safety domains

## 1. Memory (heap / RSS / buffers)
Threat: OOM from large allocs, buffer concat, cache unbounded growth.
Cap: request memory budget, streaming parsers, cache max entries/TTL, arena resets.

## 2. CPU / compute
Threat: expensive loops, password hashing floods, image rescale, sync crypto on hot path.
Cap: worker pools, cost-based rate limits, cheap-validate-first, queue expensive work.

## 3. File descriptors & handles
Threat: FD exhaustion from leaks or connection storms.
Cap: `ulimit`/rlimit awareness, pool sizes < FD budget, always close on error paths.

## 4. Threads / workers / async tasks
Threat: thread-per-request unbounded spawn; goroutine leaks.
Cap: fixed pools, semaphores, max in-flight, reject when saturated.

## 5. Connections (DB, HTTP, gRPC, Redis)
Threat: pool exhaustion; connection leak; thundering herd.
Cap: max open/idle, wait timeout, circuit breaker, checkout limits per request.

## 6. Disk / storage / inodes
Threat: fill disk with uploads/logs/temps; inode exhaustion.
Cap: upload size, retention, temp dir quotas, rotate logs, exclusive temps.

## 7. Queues / channels / buffers
Threat: unbounded queue → memory DoS; lost backpressure.
Cap: max depth; full-policy; producer checks depth; propagate pressure upstream.

## 8. Time / waits
Threat: slowloris; forever awaits; blocked shutdown.
Cap: read/write/idle timeouts; per-call deadlines; cancel propagation; shutdown drain deadline.

## 9. Rate & concurrency at the edge
Threat: flood; expensive endpoint abuse.
Cap: per-principal + per-IP (/64 for IPv6); token bucket; endpoint cost weights.

## 10. Amplification parsers
Threat: zip bombs, billion laughs, deep JSON, ReDoS, length-field over-alloc.
Cap: output size + ratio; depth; linear-time regex; validate length vs remaining bytes.

## 11. Request surface bounds
Threat: huge headers/bodies/multipart/GraphQL alias bombs.
Cap: body/header/URL/part counts; GraphQL depth/complexity; disable abusive introspection in prod as policy.

## 12. Lifecycle / ownership leaks
Threat: lost refs before close; double-responsibility confusion; error-path skips cleanup.
Cap: try/finally, defer, `with`, ownership types; leak tests.

## 13. Downstream quotas
Threat: one user burns shared vendor quota; fan-out storms.
Cap: per-tenant budgets; bulkheads; cache; coalesce; no unbounded fan-out.

## 14. GPU / accelerator (when applicable)
Threat: VRAM exhaustion; unbounded batch size.
Cap: batch caps, queue inference, timeout, memory fraction.
