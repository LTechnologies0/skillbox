# Principles 1–30 (core + extended original set)

Golden rule: *Do the absolute minimum, at the right time, in the right place, and reuse everything possible.*

## Core (1–10)

### 1. Break Your GUI into LEGO Blocks (Modularity)
**Why?** Less redundancy, more reuse, easier maintenance.
**How?**
- One component = one responsibility (button draws/clicks; logic lives outside).
- Prefer composition over deep inheritance (`IRenderable`, `IClickable`).
- Build complex UI from small reusable parts.

### 2. Only Render What’s Visible (Culling)
**Why?** Most cost is off-screen or hidden work.
**How?**
- Skip hidden / out-of-view components.
- Use clipping (`glScissor`, CSS `clip-path`, platform scissor).
- Hide (`visible = false`) instead of destroy/recreate.

### 3. Reuse Everything (Pooling & Caching)
**Why?** Create/destroy is expensive.
**How?**
- Object pools for widgets, buffers, particles.
- Texture/font atlases to cut GPU binds.
- Cache costly results (resized bitmaps, measured layouts).

### 4. Offload Heavy Tasks (Background Processing)
**Why?** UI thread must stay responsive.
**How?**
- Load I/O, decode, network, heavy math off UI thread.
- Notify via callbacks/promises/observables.
- Never `sleep`/block/long-loop on UI thread.

### 5. Optimize Rendering (Batch & GPU)
**Why?** Draw calls and state changes dominate.
**How?**
- Batch similar elements into one draw.
- Enable GPU 2D paths (OpenGL/Direct2D/Metal/Skia).
- Dirty-flag redraw: only changed widgets.

### 6. Limit Unnecessary Calculations (Lazy & Incremental)
**Why?** Full recompute every frame wastes CPU.
**How?**
- Lazy evaluate on demand.
- Incremental layout for affected subtree only.
- Precompute static geometry at startup.

### 7. Handle Events Smartly (Event Optimization)
**Why?** Event storms trigger needless work.
**How?**
- Event delegation on parents.
- Throttle/debounce scroll/resize/input.
- Spatial structures (grid/quadtree) for hit-testing.

### 8. Optimize Animations (Smooth & Light)
**Why?** Bad animation = jank and GPU load.
**How?**
- Simple easing; animate via matrix transforms.
- Prefer GPU properties; drop to 30 FPS if acceptable.
- Sprite sheets / atlases for frame animation.

### 9. Minimize Memory Usage (Memory Efficiency)
**Why?** Alloc → GC → hitch.
**How?**
- Avoid per-frame allocations; pool/static buffers.
- Prefer compact IDs (`int`/`enum` over strings).
- Share fonts/textures across widgets.

### 10. Profile and Optimize Continuously (Measure & Improve)
**Why?** You cannot optimize what you do not measure.
**How?**
- Profile CPU/GPU/memory.
- Budgets: ~16 ms/frame @60 Hz; keep UI work well under that.
- Validate on low-end hardware.

## Additional original set (11–30)

### 11. Use Data Virtualization (Virtual Lists)
**Why?** Thousands of live nodes destroy layout/memory.
**How?** Render visible window only; recycle rows; chunk-load data.

### 12. Optimize Text Rendering
**Why?** Text is a silent hotspot.
**How?** Prefer system fonts; cache static glyphs as textures; limit styles; SDF/MSDF for scale.

### 13. Reduce Layout Thrashing
**Why?** Interleaved measure/mutate forces sync layout.
**How?** Batch reads then writes; sync to rAF/vsync; avoid read-after-write layout.

### 14. Use Efficient Data Structures
**Why?** Wrong structure = slow hit-test/update.
**How?** Spatial partition; arrays for locality; hash maps for ID lookup.

### 15. Optimize Image Usage
**Why?** Images dominate bytes and decode time.
**How?** Compress; exact display resolution; sprites; lazy-load in viewport.

### 16. Minimize Style Recalculations
**Why?** Style invalidation cascades.
**How?** Classes over inline churn; animate `transform`/`opacity`; group style writes.

### 17. Use Efficient State Management
**Why?** Broad state fans out into re-renders.
**How?** Immutable/diffable state; skip unchanged subtrees; fine-grained signals/observables.

### 18. Optimize Input Handling
**Why?** Input handlers block scroll/paint.
**How?** Passive listeners where safe; debounce typing; unified pointer events.

### 19. Use Efficient Theming
**Why?** Theme bloat and deep cascades cost.
**How?** CSS variables / shared theme objects; flatten stylesheet depth.

### 20. Optimize for Accessibility
**Why?** Extra a11y work can thrash if naive.
**How?** Semantic structure; minimal ARIA; focus changes must not force full redraws.

### 21. Use Dirty Rectangles for Partial Updates
**Why?** Full framebuffer clears are wasteful for local changes.
**How?** Track dirty regions; merge overlaps; scissor GPU work to those rects.

### 22. Optimize Shaders for UI Rendering
**Why?** Shader/bind thrash kills batching.
**How?** Simple UI shaders; sort by pipeline; precompile at load.

### 23. Implement Level of Detail (LOD) for Complex UI
**Why?** Full detail when tiny is free waste.
**How?** Simpler visuals when small/far; switch by size; prebuild LOD variants.

### 24. Use Efficient Serialization for UI State
**Why?** Fat save/load stalls.
**How?** Binary/compact formats; deltas; light compression (LZ4/Zstd).

### 25. Optimize Touch and Gesture Handling
**Why?** Gesture math can burn CPU.
**How?** Platform recognizers; simple detectors; coalesce/debounce touch streams.

### 26. Use Efficient Localization
**Why?** Shipping all languages in RAM hurts.
**How?** External strings; lazy language packs; string IDs in code.

### 27. Optimize for High-DPI Displays
**Why?** 2×–3× pixels raise fill cost.
**How?** Vectors where possible; mipmaps; native resolution assets (no upscale blur).

### 28. Use Efficient Undo/Redo Systems
**Why?** Full snapshots explode memory.
**How?** Store deltas; cap history; compress stacks.

### 29. Optimize for Low-Power Devices
**Why?** Battery and thermal throttle kill UX.
**How?** Lower FPS when idle/background; power-aware EGL/display modes; batch GPU submits.

### 30. Use Predictive Loading
**Why?** Waiting on next screen feels slow.
**How?** Prefetch likely routes/assets; optional light prediction; idle-time background fetch.
