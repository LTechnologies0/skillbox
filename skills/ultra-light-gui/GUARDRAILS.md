# Guardrails — no UI degradation, no reckless removal

These rules override opportunistic “optimizations.” Performance wins that break UX, visuals, or product intent are failures.

## Absolute constraints

1. **No visual / UX regression**
   - Layout, spacing, typography, contrast, hit targets, focus order, motion intent, and information density must remain equivalent unless the user explicitly accepts a design change.
   - “Faster but uglier / emptier / harder to use” is rejected.

2. **No component removal without a modern replacement**
   - Do **not** delete buttons, panels, charts, nav, dialogs, or affordances “to go faster.”
   - Removal is allowed **only** when a **modern/optimized alternative** exists that:
     - preserves the same user goals and critical flows,
     - fits the **user’s project** (stack, design system, a11y, platform conventions),
     - is equal or better in clarity and capability for those goals.
   - Prefer: virtualize, defer, cache, simplify *implementation* — not amputate features.

3. **Respect the project’s design language**
   - Match existing tokens, components, navigation patterns, and platform norms.
   - Do not import a foreign UI kit or strip chrome to a bare prototype unless requested.

4. **Accessibility is not optional collateral**
   - Optimizations must not break keyboard, screen reader, focus, reduced-motion, or contrast requirements already present (or required by the project).

5. **Measure before and after**
   - Every change set needs a profiling baseline and a post-check (see [PROFILING.md](PROFILING.md)).
   - If metrics improve but UX checks fail → **revert or redesign**, do not ship.

## Allowed vs forbidden “simplifications”

| Allowed (keep feature, change how) | Forbidden (unless user OK + replacement) |
|------------------------------------|------------------------------------------|
| Virtualize a long list | Delete the list / pagination without equivalent browse |
| Lazy-load a heavy panel | Remove the panel from the product |
| Replace custom blur with baked 9-slice **same look** | Flatten to no elevation when design requires depth |
| Swap heavy chart lib for lighter one **same data/UX** | Drop charts “users can use the API” |
| Debounce search input | Remove search |
| LOD when zoomed out (detail returns on zoom in) | Permanently low-detail UI |

## Replacement checklist (mandatory before any removal)

If proposing to remove or replace a UI piece, answer all:

- [ ] What user job does this component serve?
- [ ] What is the proposed alternative (name + stack-native approach)?
- [ ] Does it preserve the same jobs and critical paths?
- [ ] Does it match project design system / platform patterns?
- [ ] a11y parity (roles, labels, focus, motion)?
- [ ] Visual parity accepted by user (screenshot/diff or explicit OK)?
- [ ] Perf delta proven with profiling (before/after)?

If any box is unchecked → **do not remove**; optimize in place.

## Anti-degradation verification (after every change)

Run as many as apply to the stack (details in PROFILING.md):

1. **Visual**: side-by-side or screenshot diff on key screens; no CLS / jump; no clipped text.
2. **Interaction**: primary flows still completable; tap targets ≥ project minimum.
3. **Motion**: animations still communicate state (or respect `prefers-reduced-motion`).
4. **a11y**: focus order, names, live regions still make sense.
5. **Perf**: frame time / FPS / INP / jank % improved or stable — never “faster” only by deleting work the user still needs.

## Agent language

When suggesting changes, state explicitly:

- *What is preserved* (UX/features/look).
- *What implementation changes* (technique + principle #).
- *How we will verify* (profiling methods + anti-regression checks).

Never present “remove X” as the first optimization unless the user asked to cut scope.
