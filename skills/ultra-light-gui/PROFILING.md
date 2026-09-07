# GUI / UI profiling methods (verify with the right tools)

Use profiling to **baseline → change → re-measure**. Prefer release/profile builds, not debug. Pick every method that fits the user’s stack; do not invent numbers.

## Universal checklist (all platforms)

| Check | What to capture | Pass criteria (typical) |
|-------|-----------------|-------------------------|
| Frame time / FPS | p50/p95 frame duration vs refresh period | ≤16.6 ms @60 Hz; ≤8.3 ms @120 Hz for critical motion |
| Jank / dropped frames | Missed vsync count during scroll/anim | Near-zero clusters on critical paths |
| Interaction latency | Tap → next paint / input delay | Competitive with project target (often <100–200 ms feel) |
| CPU main/UI thread | Samples during janky scenario | No long tasks monopolizing UI thread |
| GPU / fill / overdraw | Overdraw viz, GPU time | Overdraw reduced; GPU under budget |
| Memory / GC | Heap, alloc rate during scroll | No alloc storms correlating with jank |
| Startup / first paint | Cold start to interactive shell | Improved or unchanged UX path |
| Battery / thermal (mobile) | Sustained scroll / idle FPS | Idle does not spin GPU/CPU |

Always record: **device, build mode, scenario steps, before/after**.

---

## Web (browser)

| Method | Use for |
|--------|---------|
| Chrome/Edge/Firefox **Performance** panel | Main-thread long tasks, layout/paint, FPS |
| **Performance insights** / Core Web Vitals | LCP, INP, CLS |
| **Rendering** drawer: paint flashing, layer borders, FPS meter | Overpaint, layer explosion |
| **Coverage** + unused CSS/JS | Bundle bloat on critical path |
| Lighthouse (lab) | Regression gate in CI |
| `scheduler` / Long Animation Frames (where available) | Interaction attribution |
| React/Vue/etc. **profiler** + why-did-you-render style tools | Unnecessary re-renders |
| Web Vitals extension / RUM (field) | Real-user INP/LCP |

**Scenario tips:** throttle CPU 4–6×; mid-tier mobile emulation; profile scroll, open modal, typeahead.

---

## Android (View / Compose)

| Method | Use for |
|--------|---------|
| **Profile GPU Rendering** (on-device bars) | Per-frame stages vs 16 ms line |
| **Debug GPU Overdraw** | Color-coded multi-draw pixels |
| **Show hardware layers updates** / layout bounds | Unexpected invalidation |
| Android Studio **CPU Profiler** (sample/instrumented) | Hot methods on UI thread |
| **System Trace / Perfetto / Systrace** | FrameTimeline, scheduling, binder |
| **FrameMetricsAggregator** / Macrobenchmark | Automated jank & startup |
| Compose **Layout Inspector** + recomposition counts | Skip failures / storm rebuilds |
| **Macrobenchmark** + Baseline Profiles | Startup & scroll regressions in CI |
| LeakCanary / Memory Profiler | Leaks and alloc churn |

Prefer **profile/release** with tracing; confirm on physical mid/low devices.

---

## iOS / macOS

| Method | Use for |
|--------|---------|
| Xcode **Instruments → Time Profiler** | CPU hotspots |
| **Core Animation** / OS Signposts | FPS, commit, render server |
| **Animation Hitches** / Hitches graph | Jank clusters |
| **Metal System Trace** | GPU, bandwidth, shaders |
| View debugger / view hierarchy | Overly deep trees |
| Allocations / Leaks / VM Tracker | Memory pressure |
| XCTest **XCTMetric** / Measure | Automated frame & launch metrics |

---

## Flutter

| Method | Use for |
|--------|---------|
| `flutter run --profile` + **DevTools Performance** | UI vs raster thread frames |
| Performance **overlay** | Live jank while reproducing |
| **Highlight repaints** / rebuild tracking | Repaint storms |
| CPU + Memory profilers in DevTools | Dart hotspots / GC |
| Native profilers (AS / Instruments) | Platform channel cost |
| Chrome DevTools (Flutter **web**) | Web timeline (not Dart DevTools profile mode) |

---

## Game / custom / immediate-mode (ImGui, Skia, custom engines)

| Method | Use for |
|--------|---------|
| In-engine **frame time HUD** + histograms | Budget adherence |
| GPU vendor tools (Nsight, RGP, Xcode Metal, RenderDoc) | Draw calls, overdraw, bandwidth |
| CPU sampling (perf, VTune, Tracy, Optick) | Widget submission cost |
| Tracy/Optick **zones** around layout/draw | Phase costs |
| Mesh/texture memory stats | Atlas / leak growth |
| Input latency tests (high-speed capture or platform APIs) | Click-to-photon style checks |

---

## Desktop toolkits (Qt, GTK, WinUI, Electron, Tauri)

| Method | Use for |
|--------|---------|
| Toolkit / IDE profilers (Qt Creator, VS) | Event & paint costs |
| Platform GPU capture (PIX, RenderDoc, Instruments) | Compositor/GPU |
| Electron: Chromium Performance + `electron` tracing | Same as web + main process |
| `perf` / ETW / Instruments on host | OS scheduling & I/O |

---

## What “verified with all methods” means in practice

The agent must **not** claim a single tool is enough. For the user’s stack:

1. List **applicable** methods from the tables above.
2. Run (or instruct) at least:
   - one **frame/jank** measure,
   - one **CPU or GPU** deep dive matching the suspected bottleneck,
   - one **memory** glance if scroll/alloc suspected,
   - **anti-degradation** visual + interaction checks ([GUARDRAILS.md](GUARDRAILS.md)).
3. Report before/after with the same scenario script.

If a tool is unavailable in the environment, say so and give the exact command/UI path — do not skip the category silently.

## Minimal scenario script template

```text
Device / OS / build mode:
Scenario:
  1. Cold start → first interactive screen
  2. Scroll primary list 5s (fast + slow)
  3. Open/close heaviest panel or modal
  4. Primary create/edit action
  5. Idle 5s (CPU/GPU should settle)
Metrics: FPS/frame time, jank, interaction latency, memory delta
UX: screenshot/diff, flow completable, a11y smoke
```
