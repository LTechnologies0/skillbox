# Universal methods (apply across domains)

| Method | Intent |
|--------|--------|
| **Inventory & budget** | List scarce resources; assign numeric caps per trust tier |
| **Bound capacity** | Max size/count/depth/concurrency — never “unlimited” on untrusted paths |
| **Streaming enforcement** | Enforce limits while reading, not after full buffer |
| **Timeouts** | Every listen + every outbound call has a deadline |
| **Cancellation propagation** | context / AbortSignal / CancelledError end abandoned work |
| **Bounded queues** | Capacity + full-policy (block/drop/reject) |
| **Backpressure** | Full buffer signals upstream; edge paces or sheds |
| **Load shedding** | Prefer 429/503 over degrading into meltdown |
| **Rate limiting** | Per-principal primary; IP secondary; cost-weighted endpoints |
| **Bulkheads** | Isolate pools so one feature cannot starve others |
| **Circuit breaker** | Stop calling sick deps; fail fast |
| **Cheap-first** | Validate/auth cheap checks before expensive work |
| **Amplification asymmetry** | Reject when attacker cost ≪ defender cost |
| **Retry budgets** | Cap retries; exponential backoff + jitter; idempotency |
| **Lifecycle discipline** | Single owner closes/returns resource on all paths |
| **OS quotas** | rlimit/cgroup/container limits as outer fence |
| **Observability** | Saturation metrics: pool wait, queue depth, shed count, RSS, FD |
| **Graceful shutdown** | Stop intake → drain with deadline → cancel → release |
| **Fail closed on saturation** | Don’t accept work you cannot bound |
| **Adaptive concurrency** | Optional: limit in-flight from latency/error signals |

## Ordering when remediating

1. Unbounded → bounded  
2. Missing timeouts/cancel  
3. Amplification parsers  
4. Rate limit + shed  
5. Bulkheads + breaker  
6. OS/cgroup fences  
