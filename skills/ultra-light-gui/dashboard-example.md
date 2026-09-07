# Example: optimizing a complex dashboard

**Problem:** Real-time dashboard (charts, tables, gauges) feels laggy.

## Applied principles (mix of 1–30 and 31–100)

1. **Modularity (#1)** — Split into independent widgets.
2. **Culling (#2) + content-visibility (#31)** — Skip off-screen widgets.
3. **Pooling (#3) + view recycling (#44)** — Reuse widget/row instances.
4. **Background (#4) + async decode (#99)** — Fetch/parse off UI thread.
5. **Batch (#5) + instancing (#58)** — One pipeline for chart marks.
6. **Lazy (#6) + shell-first (#100)** — Load widget data when visible.
7. **Events (#7) + coalesced pointers (#89)** — Delegate; sample once/frame.
8. **Dirty rects (#21) + damage (#39–41)** — Redraw only updated widgets.
9. **LOD (#23)** — Simplified charts when zoomed out / small.
10. **Predictive (#30) + priorities (#50)** — Prefetch next tabs in background.
11. **Shaders (#22) + state-switch cut (#78)** — Shared shader; sort by texture.
12. **Delta serialization (#24)** — Stream widget updates, not full state.
13. **Frame budget (#46–48)** — Cap work per vsync; drop late frames.
14. **Yield / paint-before-work (#49–51)** — Immediate filter feedback, then compute.
15. **Overdraw cut (#36–37)** — Opaque panels; fewer translucent overlays.
16. **Cached passes (#68–69)** — Retain static sidebars as layers.
17. **Virtual table (#11, #42)** — Window + overscan for large grids.
18. **MSDF / atlas (#59–65)** — Shared text/icon atlas; dirty uploads only.

**Guardrails:** Keep every widget the product needs; virtualize/defer/cache instead of deleting. Re-run the same scroll/open-panel scenario under GPU overdraw + frame timeline + memory before calling it done ([GUARDRAILS.md](GUARDRAILS.md), [PROFILING.md](PROFILING.md)).

**Target result:** Stable 60 FPS on mid-range devices, interaction latency typically <50 ms after paint-first feedback — with visual/UX parity.
