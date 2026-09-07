# Summary: 100 ultra-light GUI principles (one sentence each)

| # | Principle | Rule |
|---|-----------|------|
| 1 | Modularity | Break GUI into reusable single-responsibility components. |
| 2 | Culling | Only render what is visible. |
| 3 | Pooling & caching | Reuse objects and cache expensive results. |
| 4 | Background processing | Offload heavy work from the UI thread. |
| 5 | Batch rendering | Group draws to minimize GPU overhead. |
| 6 | Lazy & incremental | Compute only what is needed, when needed. |
| 7 | Event optimization | Delegate, throttle, and accelerate hit-testing. |
| 8 | Light animations | GPU transforms, simple easing, sprite sheets. |
| 9 | Memory efficiency | Minimize allocations; share resources. |
| 10 | Profiling | Measure bottlenecks, then optimize. |
| 11 | Data virtualization | Render only visible list/table rows. |
| 12 | Text rendering | Limit fonts; cache glyphs; use SDF/MSDF. |
| 13 | Layout thrashing | Batch reads/writes; sync to vsync/rAF. |
| 14 | Data structures | Spatial partitions + cache-friendly arrays. |
| 15 | Image optimization | Compress, exact size, sprites, lazy-load. |
| 16 | Style recalculation | Classes + `transform`/`opacity`; group writes. |
| 17 | State management | Fine-grained updates; skip unchanged trees. |
| 18 | Input handling | Passive listeners, debounce, pointer events. |
| 19 | Theming | Shared variables/objects; flat styles. |
| 20 | Accessibility | Semantic UI; lean ARIA; cheap focus updates. |
| 21 | Dirty rectangles | Redraw only changed regions. |
| 22 | Shader optimization | Simple shaders; batch pipeline state. |
| 23 | UI LOD | Simpler visuals when small/far. |
| 24 | Serialization | Binary, deltas, light compression. |
| 25 | Gestures | Lightweight / hardware-assisted recognition. |
| 26 | Localization | Externalize strings; lazy language packs. |
| 27 | High-DPI | Vectors, mipmaps, native resolution. |
| 28 | Undo/redo | Deltas + capped history. |
| 29 | Low power | Lower FPS when idle; batch GPU wakeups. |
| 30 | Predictive loading | Prefetch likely next UI/data. |
| 31 | content-visibility | Defer off-screen rendering work. |
| 32 | CSS/layout contain | Isolate invalidation to subtrees. |
| 33 | Intrinsic size hints | Prevent jump when content is deferred. |
| 34 | Compositor motion | Animate/scroll via transforms, not layout. |
| 35 | Layer budget | Promote sparingly; avoid layer explosion. |
| 36 | Overdraw cut | Opaque front-to-back; flatten stacks. |
| 37 | Blend sparingly | Prefer opaque/additive over alpha piles. |
| 38 | Occlusion skip | Do not submit fully covered UI. |
| 39 | Damage tracking | Skip frames with no dirty regions. |
| 40 | Partial present | Tell compositor which rects changed. |
| 41 | Buffer age | Reuse valid back-buffer pixels. |
| 42 | List overscan | Extra off-screen rows for fast scroll. |
| 43 | Compositor scroll | Keep scroll positioning off main thread. |
| 44 | View recycling | Rebind pooled rows instead of recreate. |
| 45 | Async observers | Measure visibility/size without poll thrash. |
| 46 | Frame budget | Stay under 16.6 ms (60 Hz) / 8.3 ms (120 Hz). |
| 47 | VSync align | Clock UI to display refresh signals. |
| 48 | Frame pacing | Drop late frames; keep motion consistent. |
| 49 | Main-thread yield | Chunk long tasks for responsiveness. |
| 50 | Task priorities | User-blocking vs background explicitly. |
| 51 | Paint-before-work | Show feedback before heavy compute. |
| 52 | Phase invalidation | Invalidation scoped to compose/layout/draw. |
| 53 | Skip subtrees | Bypass unchanged declarative nodes. |
| 54 | Derived state | Compute at cheap subscription boundaries. |
| 55 | Draw-phase anim | Keep anim reads out of full rebuilds. |
| 56 | Flatten trees | Fewer nested wrappers → fewer measures. |
| 57 | Single-pass measure | Avoid nested double-measure layouts. |
| 58 | Instancing | One draw for many identical quads/glyphs. |
| 59 | Glyph LRU atlas | Fixed VRAM budget for text glyphs. |
| 60 | MSDF text | Scale text without re-raster storms. |
| 61 | Font/icon subset | Ship only used codepoints/icons. |
| 62 | Glyph pre-warm | Rasterize chrome glyphs before first use. |
| 63 | Atlas packing | Shelf/MaxRects + padding against bleed. |
| 64 | Multi-page atlas | Spill pages instead of giant textures. |
| 65 | Dirty atlas upload | Sub-image upload changed tiles only. |
| 66 | Tight meshes | Cut transparent overdraw in sprites. |
| 67 | Shade-once | Avoid restading covered translucent pixels. |
| 68 | Cached render passes | Reuse undamaged filter/blur layers. |
| 69 | Retained layers | Rasterize once; move via composite. |
| 70 | Diff/patch grids | Upload only dirty cells/spans. |
| 71 | Buffering tradeoff | Balance smoothness vs input latency. |
| 72 | Adaptive Hz | Match focused refresh; save power idle. |
| 73 | No UI MSAA tax | Skip heavy AA/post on flat UI. |
| 74 | Compact formats | R8/compressed textures over RGBA8 spam. |
| 75 | CPU rect occlusion | Clip axis-aligned layers before compose. |
| 76 | IMGUI early-out | Skip widgets in collapsed/clipped windows. |
| 77 | List clipper | Emit only visible immediate-mode items. |
| 78 | State-switch cut | Group by texture/clip/style for batches. |
| 79 | Draw channels | Preserve z-order without breaking batches. |
| 80 | Zero-alloc idle | Quiet frames allocate nothing. |
| 81 | Frame arenas | Bump-allocate temps; free en masse. |
| 82 | String discipline | Intern/format only on data change. |
| 83 | Baked constraints | Precompute static layout graphs. |
| 84 | Skeletons | Cheap placeholders beat blank waits. |
| 85 | Above-the-fold first | Critical path before the rest. |
| 86 | UI code-split | Lazy-load non-critical surfaces. |
| 87 | Time-sliced layout | Yield during huge measure passes. |
| 88 | Spatial hit-hash | Fast pick among dense controls. |
| 89 | Coalesced pointers | One drag sample set per frame. |
| 90 | Coalesce invalidations | Merge dirty marks; single flush. |
| 91 | Less live blur | Bake or limit soft shadows/blurs. |
| 92 | Cache backdrop-filter | Do not reblur scrolling underlays. |
| 93 | Baked 9-slice chrome | Pre-render borders/shadows. |
| 94 | 9-slice over tessellate | Stretch assets instead of retessellating. |
| 95 | Path tessellation cache | Retain vector meshes across frames. |
| 96 | Scissor first | Coarse clip before expensive masks. |
| 97 | Shared masks | Reuse stencil/mask across twins. |
| 98 | No layout read storms | Never measure immediately after mutate. |
| 99 | Async image decode | Keep decode off the UI thread. |
| 100 | Shell-first start | Hydrate heavy UI after first paint. |
