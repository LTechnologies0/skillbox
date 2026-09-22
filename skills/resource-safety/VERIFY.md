# Verify resource safety

| Test | Expect |
|------|--------|
| Oversized body/stream | Reject mid-stream; memory flat |
| Zip/XML/JSON bomb fixtures | Cap hit; no OOM |
| Evil regex + long input | Linear time or hard kill; no event-loop freeze |
| Saturate workers | 429/503; queue depth capped |
| Slow dependency (hold TCP) | Client timeout; pool not exhausted forever |
| Cancel/abort mid-request | Upstream work stops; no leak growth |
| FD leak soak | FD count stable after N requests |
| Cache soak | Entries ≤ max; RSS bounded |
| Retry storm | Backoff; budget stops amplification |
| Shutdown under load | Drain then exit within deadline |

Metrics to watch: queue depth, pool active/wait, shed rate, p99 latency, RSS, FD count, goroutine/thread count.
