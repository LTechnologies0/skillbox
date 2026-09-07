# Principles 31–100 (research supplement)

English-keyword research themes used: *virtual scrolling / overscan*, *content-visibility / CSS contain*, *compositor layers*, *overdraw / occlusion / early-Z*, *damage tracking / partial present / buffer age*, *frame budget / vsync / Choreographer*, *INP / scheduler.yield*, *Compose phases / skippable recomposition*, *glyph atlas / MSDF / shelf packing*, *ImGui ListClipper / early-out*, *instanced quads*, *backdrop-filter cost*, *cold start / progressive hydration*.

These extend principles 1–30 — apply only when the bottleneck matches.

---

### 31. Defer Off-Screen Rendering (`content-visibility`)
**Why?** Large DOM/widget trees pay style/layout/paint even when invisible.
**How?** Mark below-fold sections skippable until near viewport (`content-visibility: auto` or toolkit equivalent). Never on critical above-the-fold UI.

### 32. Establish Containment Boundaries (`contain`)
**Why?** Local changes should not invalidate the whole tree.
**How?** Promise layout/paint/size isolation on cards/lists (`contain: layout paint` or platform “clip + isolate”).

### 33. Reserve Intrinsic Size for Deferred Content
**Why?** Deferred nodes collapse to 0 → scrollbar jump / CLS.
**How?** Pair deferred rendering with size hints (`contain-intrinsic-size`, estimated row height, placeholders).

### 34. Prefer Compositor-Only Motion
**Why?** Layout-affecting animation blows the frame budget.
**How?** Move/scroll with `transform` / matrix / layer offset — not `top`/`left`/`margin` every frame.

### 35. Control Layer Promotion (Avoid Layer Explosion)
**Why?** Too many compositor layers = memory + bandwidth tax.
**How?** Promote only scrolling/animating surfaces; demote static chrome; audit layer count in profiler.

### 36. Reduce Overdraw (Front-to-Back Opaque First)
**Why?** Painting the same pixel many times kills fill rate (esp. mobile).
**How?** Draw opaque UI front-to-back; flatten stacked full-screen panels; visualize overdraw.

### 37. Prefer Opaque or Cheaper Blends Over Alpha Stacks
**Why?** Alpha blending reads destination + math per pixel.
**How?** Opaque quads when possible; additive if acceptable; fewer translucent layers.

### 38. Occlusion / Early Rejection for Covered UI
**Why?** Hidden-behind-modal content still costs if submitted.
**How?** Skip fully occluded subtrees; optional depth/HiZ or CPU rectangle occlusion for layered UIs.

### 39. Damage Tracking — Skip Undamaged Frames
**Why?** Idle UI should do ~zero GPU work.
**How?** Propagate dirty flags up the tree; if no damage, skip render-pass build (browser compositor / custom engine pattern).

### 40. Partial Present / Swap-with-Damage
**Why?** Compositors can avoid recomposing unchanged regions.
**How?** Report damage rects on present (`eglSwapBuffersWithDamage`, platform “dirty region” APIs).

### 41. Buffer Age / Reuse Valid Back-Buffer Regions
**Why?** Full clear+redraw wastes bandwidth when only a corner changed.
**How?** Query buffer age; redraw only damaged + aged regions; keep contents valid outside damage.

### 42. Virtual List Overscan Buffer
**Why?** Exact-viewport windows flash blank during fast scroll.
**How?** Render N extra rows above/below viewport; tune overscan vs CPU.

### 43. Scroll on the Compositor Thread
**Why?** Main-thread scroll positioning causes jank.
**How?** Position windows with GPU transforms; keep scroll handlers thin (passive + rAF).

### 44. Recycle Nodes / Views Aggressively
**Why?** Alloc+attach during scroll → GC and inflation spikes.
**How?** Keep a small pool of row views; rebind data; avoid destroying on every window move.

### 45. Observe Size/Visibility Asynchronously
**Why?** Sync measure loops thrash layout.
**How?** `ResizeObserver` / `IntersectionObserver` (or platform analogs) instead of polling geometry.

### 46. Respect the Frame Budget
**Why?** Missed deadlines = dropped frames (jank).
**How?** Budget ~16.6 ms @60 Hz, ~8.3 ms @120 Hz; leave headroom for system compositor.

### 47. Align Work to VSync Signals
**Why?** Rendering off-cadence causes judder.
**How?** Drive updates from Choreographer / CADisplayLink / display link / `requestAnimationFrame`.

### 48. Pace Frames — Drop Late Work Cleanly
**Why?** Queuing late frames increases latency and stutters.
**How?** If behind vsync, skip a frame and advance animation time consistently (don’t “catch up” with burst draws).

### 49. Yield the Main Thread on Long Tasks
**Why?** >50 ms tasks delay input and next paint (INP).
**How?** Chunk work; `scheduler.yield()` / cooperative yield; never monopolize UI thread.

### 50. Prioritize Tasks Explicitly
**Why?** Background work should not preempt interaction.
**How?** `scheduler.postTask` priorities / platform QoS: user-blocking for feedback, background for prefetch.

### 51. Paint Feedback Before Heavy Work
**Why?** Users feel latency before results finish.
**How?** On click: update pending UI → yield/paint → then compute/filter/render results.

### 52. Phase-Scoped Invalidation (Compose / Pipeline Phases)
**Why?** A scroll offset change should not rebuild the whole tree.
**How?** Separate composition / layout / draw; read high-frequency state in the cheapest phase that needs it.

### 53. Skip Unchanged Subtrees
**Why?** Declarative UI re-executes unless guarded.
**How?** Stable props, equality checks, skippable composables, `shouldComponentUpdate` / memo equivalents.

### 54. Derive State at a Cheap Boundary
**Why?** Raw high-frequency state in composition invalidates too much.
**How?** `derivedStateOf` / memoized selectors; push transforms next to consumers.

### 55. Animate in Draw/Layer Phase When Possible
**Why?** Animation state in composition forces full rebuilds.
**How?** Read anim values in draw modifiers / layer properties so layout/composition can skip.

### 56. Flatten Widget / View Hierarchies
**Why?** Deep trees multiply measure passes.
**How?** Fewer wrappers; merge decorative containers; prefer constraint-based single-pass layouts.

### 57. Avoid Extra Measure Passes
**Why?** Nested weights / double measure explode CPU.
**How?** Fixed sizes where possible; one-pass constraints; cache child intrinsic sizes.

### 58. Instance Repeated Quads / Glyphs
**Why?** One draw call per cell does not scale.
**How?** GPU instancing for identical UI primitives (cells, ticks, icons) with per-instance attributes.

### 59. Glyph Atlas with Fixed VRAM Budget (LRU)
**Why?** Unlimited glyph textures thrash memory.
**How?** Pack glyphs into atlas pages; LRU-evict under a byte budget; miss → generate once.

### 60. Prefer MSDF/MTSDF for Scalable UI Text
**Why?** Re-rasterizing glyphs per size is costly.
**How?** Multi-channel signed distance fields; one atlas samples crisply across scales.

### 61. Subset Fonts and Icon Sets
**Why?** Full Unicode / huge icon fonts bloat download and atlas.
**How?** Ship used codepoints only; per-locale subsets; tree-shake icon packs.

### 62. Pre-Warm Critical Glyphs at Startup
**Why?** First-sighting raster stalls the first animation.
**How?** Warm ASCII / UI chrome glyphs during splash/idle before interactive use.

### 63. Pack Atlases with Shelf or MaxRects
**Why?** Naive packing wastes VRAM and causes more pages.
**How?** Height-sorted shelf packing for glyphs; MaxRects for mixed sprite sizes; padding against bleed.

### 64. Multi-Page Atlases When One Texture Overflows
**Why?** Giant single textures hit GPU limits and upload cost.
**How?** Spill to page 2..N; batch draw by atlas page to limit binds.

### 65. Upload Only Dirty Atlas Regions
**Why?** Full atlas reupload each new glyph is wasteful.
**How?** Mark dirty tiles/rects; sub-image upload; rebuild GPU texture sparingly.

### 66. Trim Transparent Texels / Use Tight Meshes
**Why?** Full-quad sprites overdraw empty alpha.
**How?** Tight geometry around opaque pixels; remove 100% transparent padding.

### 67. Shade Covered Pixels Once
**Why?** Stacked translucent UI pays fragment cost repeatedly.
**How?** Where applicable: opaque prepass / mask path / visibility-style “shade once”; flatten designs.

### 68. Skip Cached Undamaged Render Passes
**Why?** Filters/blur layers are expensive every frame.
**How?** Cache render-pass textures; reuse when subtree has no damage (Chromium-style pass skip).

### 69. Explicit Retained Layers for Movable Chrome
**Why?** Re-recording static content while dragging is waste.
**How?** Rasterize panel once into a layer; composite with transform while moving.

### 70. Diff / Patch Streams for Grid or Terminal UIs
**Why?** Rewriting the whole grid each frame wastes bandwidth.
**How?** Emit dirty spans/rows; upload only changed instance slices.

### 71. Choose Buffering vs Latency Deliberately
**Why?** Triple buffering smooths FPS but can add input lag.
**How?** Prefer low-latency present for interactive tools; allow deeper queues for video-like UI.

### 72. Match Refresh Rate (Adaptive Hz)
**Why?** Cap at 60 on a 120 Hz display leaves smoothness on the table — or burns power needlessly.
**How?** Query display Hz; render at native rate when focused; drop when idle (see #29).

### 73. Avoid Heavy MSAA / Post-Process on Flat UI
**Why?** UI is mostly axis-aligned; MSAA/post rarely worth the cost.
**How?** Disable MSAA for opaque UI; reserve FXAA/post for rare cases; prefer SDF edges.

### 74. Pick Compact Texture Formats
**Why?** RGBA8 everywhere wastes bandwidth.
**How?** R8/alpha atlases for glyphs; compressed color (ETC/ASTC/BC) for photos; HDR only if needed.

### 75. CPU Occlusion for Axis-Aligned Layers
**Why?** Compositing many translucent layers is slower than clipping on CPU for rectangles.
**How?** Subtract occluded rects before upload (WebRender-style rectangle occlusion) when layer count is small.

### 76. Immediate-Mode Early-Out
**Why?** Collapsed/clipped windows still run widget code if unchecked.
**How?** If `Begin`/`SkipItems` says invisible, return before expensive UI emission (Dear ImGui pattern).

### 77. List Clippers for Immediate-Mode Lists
**Why?** Submitting 100k items/frame is CPU death.
**How?** Clipper computes visible index range; only emit those items (+ overscan).

### 78. Minimize Draw-List State Switches
**Why?** Texture/clip/shader changes split batches.
**How?** Group by texture and clip; push/pop styles around loops, not per item.

### 79. Draw Channels / Splitters for Z Without Extra Draws
**Why?** Naive z-order forces texture thrash.
**How?** Split channels, emit out of order, merge to preserve batching (ImDrawListSplitter pattern).

### 80. Zero-Allocation Idle Frames
**Why?** Idle allocs still cause eventual GC jank.
**How?** Frame arenas reset each frame; no malloc on quiet frames; pool temp strings/buffers.

### 81. Bump / Arena Allocators for Ephemeral UI
**Why?** General-purpose allocators are slow for thousands of tiny nodes.
**How?** Per-frame bump allocator for layout temps; free all at once end-of-frame.

### 82. Intern Strings and Avoid Per-Frame Formatting
**Why?** `sprintf`/concat each frame allocates and fragments.
**How?** Intern labels; format only when data changes; reuse text buffers.

### 83. Precompute Layout Constraints
**Why?** Solving the same flex/grid every frame is waste.
**How?** Bake static constraint graphs; recompute only dirty branches (#6, #13).

### 84. Skeleton / Placeholder UI for Perceived Speed
**Why?** Blank screens feel slower than structured placeholders.
**How?** Show cheap skeletons immediately; replace with real content as ready (#30, #100).

### 85. Critical Path / Above-the-Fold First
**Why?** Loading everything blocks first interaction.
**How?** Prioritize chrome + first viewport assets; defer below-fold modules.

### 86. Code-Split Non-Critical UI Surfaces
**Why?** Giant bundles delay parse/compile on UI thread.
**How?** Lazy routes/panels; dynamic import; platform feature modules.

### 87. Time-Slice Enormous Layout Passes
**Why?** One 100 ms layout freezes input.
**How?** Interruptible/measure-in-chunks with yield (#49); show partial results.

### 88. Spatial Hash for Dense Hit Targets
**Why?** O(n) hit-test fails with thousands of controls.
**How?** Uniform grid / hash of bounding boxes; rebuild on sparse dirty moves (#14).

### 89. Pointer Capture and Coalesced Events
**Why?** Flood of move events overwhelms handlers.
**How?** Capture pointer during drag; process coalesced samples once per frame.

### 90. Cap Event Queue / Coalesce Redundant Invalidations
**Why?** Bursts mark the same region dirty hundreds of times.
**How?** Merge invalidations; single rAF flush; drop superseded events.

### 91. Minimize Soft Shadows and Blurs
**Why?** Blur = large kernels + overdraw.
**How?** Prefer hard edges or baked shadow sprites; limit live blur radius/layers.

### 92. Cache Backdrop Filters
**Why?** `backdrop-filter` re-samples underlay every frame.
**How?** Avoid on scrolling content; cache filtered texture until underlay damages (#68).

### 93. Bake Decorations into 9-Slice Assets
**Why?** Runtime rounded-rect + shadow tessellation costs CPU.
**How?** Pre-render borders/shadows into 9-slice / region assets; stretch at runtime.

### 94. Prefer 9-Slice / Border Images Over Per-Frame Shape Tessellation
**Why?** Tessellating curves every frame does not scale.
**How?** Static meshes for chrome; regenerate only on theme/size class change.

### 95. Cache Vector Path Tessellation
**Why?** SVG/path flattening is expensive.
**How?** Tessellate once; retain GPU buffers; invalidate on scale/theme only (#27).

### 96. Scissor Before Expensive Clips and Masks
**Why?** Full-screen masks waste fill.
**How?** Coarse scissor/rect clip first; stencil/mask only the remainder.

### 97. Reuse Stencil / Mask State Across Similar Widgets
**Why?** Rebuilding masks per control splits batches.
**How?** Share mask textures for repeated shapes; batch identically masked widgets.

### 98. Ban Sync Layout Read Storms
**Why?** `getBoundingClientRect` / measure after mutate forces layout.
**How?** One measure pass; cache rects; schedule reads before writes (#13).

### 99. Decode Images Off the UI Thread
**Why?** Decode on UI thread freezes scroll.
**How?** `createImageBitmap` / background decode / platform async image pipeline; swap in when ready.

### 100. Progressive Hydration / Shell-First Cold Start
**Why?** Inflating the full UI tree blocks first frame.
**How?** Mount shell + critical widgets first; hydrate heavy panels idle/after interaction (#6, #85).
