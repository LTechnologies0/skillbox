---
name: ultra-light-gui
description: >-
  Applies the Universal Golden Rule for ultra-light GUIs and 100 performance
  principles with hard guardrails: no UI degradation, no component removal
  without a modern project-fitting alternative, and verification via full GUI
  profiling methods (DevTools, GPU overdraw, Perfetto, Instruments, Flutter
  DevTools, RenderDoc, etc.). Use when optimizing UI/GUI performance, fixing
  jank/lag/FPS, or when the user mentions ultra-light GUI, overdraw, virtual
  lists, frame budget, or UI regression-safe optimization.
---

# Universal Golden Rule for Ultra-Light GUI

> **Do the absolute minimum, at the right time, in the right place, and reuse everything possible.**

## Guardrails (non-negotiable)

Read [GUARDRAILS.md](GUARDRAILS.md) before proposing cuts.

1. **No UI degradation** — visuals, UX flows, hit targets, a11y, and design intent stay equivalent unless the user explicitly accepts a design change.
2. **No component removal without replacement** — never delete UI to “go faster.” Removal only if a **modern/optimized alternative** preserves user jobs and **fits the user’s project** (stack, design system, platform). Prefer virtualize / defer / cache / rewrite internals.
3. **Verify with profiling** — baseline → change → re-measure using the methods in [PROFILING.md](PROFILING.md). Perf win + UX fail = revert.

## Agent protocol

When helping with GUI performance:

1. **Restate the bottleneck** (CPU layout, GPU fill, main-thread JS, alloc/GC, I/O).
2. **Baseline** with applicable profiling methods for the user’s stack ([PROFILING.md](PROFILING.md)).
3. **Pick 3–7 principles** from the catalog that match the bottleneck — do not dump all 100.
4. **Order by leverage**: measure → cull/skip → reuse → batch → offload → polish — **without removing features**.
5. **Propose concrete changes** that preserve UX; if replacing a component, complete the replacement checklist in GUARDRAILS.md.
6. **Verify after**:
   - anti-degradation checks (visual, interaction, a11y),
   - same profiling scenario before/after,
   - report what was preserved vs what implementation changed.

Prefer stack-native APIs. Keep advice framework-agnostic unless the user names a stack.

## Catalog map

| Range | File | Focus |
|-------|------|--------|
| 1–10 | [principles-01-30.md](principles-01-30.md) | Core: modularity, cull, pool, threads, batch, lazy, events, anim, memory, profile |
| 11–30 | [principles-01-30.md](principles-01-30.md) | Virtual lists, text, layout thrash, images, state, input, dirty rects, shaders, LOD, power |
| 31–100 | [principles-31-100.md](principles-31-100.md) | Compositor, overdraw, damage/partial present, frame budget, yield/INP, atlases, IMGUI, cold start |
| All | [SUMMARY.md](SUMMARY.md) | One-sentence table (100 rows) |
| Guardrails | [GUARDRAILS.md](GUARDRAILS.md) | No degradation; no reckless removal; replacement checklist |
| Profiling | [PROFILING.md](PROFILING.md) | Web / Android / iOS / Flutter / game / desktop methods |
| Example | [dashboard-example.md](dashboard-example.md) | Complex dashboard walkthrough |

## Decision cheatsheet

| Symptom | Reach for |
|---------|-----------|
| Huge lists scroll jank | 11, 31–34, 42–44, 77 |
| Full-screen redraw for tiny change | 21, 39–41, 68–69 |
| GPU fill-rate / heat | 35–38, 66, 91–93 |
| Tap feels laggy (INP) | 49–51, 4, 18 |
| Text looks soft / expensive | 12, 59–65 |
| Idle still burns CPU | 29, 40, 80 |
| Startup slow | 85–86, 100, 6 |

## Hard rules

- Never optimize without a measured or strongly evidenced bottleneck.
- Prefer **skipping work** over making work faster — skip **off-screen / redundant** work, not user-facing features.
- Prefer **reuse** (pools, atlases, cached layers) over allocate/destroy.
- Prefer **compositor/GPU transforms** over layout invalidation for motion.
- Cap ambition: ship the smallest set of principles that restore the frame budget **without UX regression**.
- Never ship “faster” by deleting UI the product still needs.
