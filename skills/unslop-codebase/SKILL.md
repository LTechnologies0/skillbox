---
name: unslop-codebase
description: >-
  Unslops any-language codebase into a clear graph/tree/branch modular
  structure: full inventory of classes, types, and data structures plus import
  usage; deep project understanding before any rewrite; rich in-code docs
  (Javadoc, KDoc, docstring, rustdoc, godoc, XML docs, etc.). Use when the
  user says unslop, deslop, declutter, structured rewrite, modularize for
  readability, or asks to make a messy codebase maintainable by anyone.
---

# Unslop a Codebase (any language)

## Goal

Rewrite the codebase into a **graph / tree / branch / modular / structured** style so that:

- Reading the code feels like walking a clear tree (roots → branches → leaves).
- Modules have one job; dependencies form an acyclic, intentional graph.
- **Anyone** can maintain it: names, shapes, and docs carry the mental model.
- Every class, type, and data structure is **documented in-code** with the language’s native rich doc syntax.

This is **clarity and maintainability**, not drive-by micro-optimizations or feature cuts.

## Non-negotiable order of operations

```text
1. Understand  →  2. Inventory  →  3. Propose graph plan  →  4. Wait for approval
        ↓
5. Rewrite by domain branches  →  6. Document every type  →  7. Verify build/tests
```

**Do not rewrite, move, or delete substantial code before steps 1–4 are done and the user has approved the plan.**

Details:

| Step | File |
|------|------|
| How to understand the project | below + [INVENTORY.md](INVENTORY.md) |
| Target writing style | [GRAPH-STYLE.md](GRAPH-STYLE.md) |
| Doc comments per language | [DOC-COMMENTS.md](DOC-COMMENTS.md) |

---

## Phase 1 — Understand the whole project (before action)

Build a working mental model. Prefer evidence from the repo over assumptions.

1. **Entry points** — `main`, app bootstrap, CLI, server listen, library public API.
2. **Build & run** — build tool, test command, package layout (do not invent).
3. **Domains** — what problem areas exist (auth, UI, IO, domain rules, …).
4. **Data flow** — input → transform → persist/output for critical paths.
5. **External deps** — what the project already uses; do not replace stacks casually.
6. **Constraints** — existing style, architecture rules, public APIs that must stay stable.

If something is unknown, say: **Definition not found in context** — then search or ask. Never invent files, types, or APIs.

**Deliverable:** a short “Project understanding” summary (domains, entry points, critical flows, risks). Show it to the user before rewriting.

---

## Phase 2 — Full inventory (classes, types, structures, imports)

Enumerate **the entire codebase** (or the scope the user named). See [INVENTORY.md](INVENTORY.md).

For every type-like entity (class, struct, enum, interface, trait, type alias, record, …):

- Name, kind, file path
- Fields / variants / type parameters
- Inheritance / implements / bounds
- **All import / use sites** (who depends on it)
- Role in the graph (root, branch, leaf, shared kernel, adapter)

**Deliverable:** inventory table or structured list + import/dependency notes. Incomplete inventory → do not start the rewrite.

---

## Phase 3 — Graph / tree rewrite plan (approval gate)

Propose the target module tree **before** editing:

```text
root (app or library façade)
├── domain/          # pure rules & types (no IO)
├── application/     # use-cases / orchestration
├── adapters/        # UI, HTTP, DB, files (edges of the graph)
└── shared/          # truly shared primitives only
```

Adapt names to the user’s language and existing conventions (Clean/Hexagonal/feature folders are fine if they match the project).

For each move/split/merge: **why**, **what breaks**, **how tests catch it**.

**Stop and wait for user approval** of this plan. Then execute.

---

## Phase 4 — Rewrite in structured style

Follow [GRAPH-STYLE.md](GRAPH-STYLE.md):

- One module ≈ one branch of the tree; one type ≈ one clear responsibility.
- Prefer composition and small focused types over god-objects.
- Make dependency direction **inward** toward domain (or the project’s agreed core).
- Keep behavior equivalent unless the user asked for behavior changes.
- Preserve public API contracts unless the user allows breaking changes.
- Prefer editing/organizing existing modules over scattering tiny files without reason; split when a file mixes unrelated domains or exceeds clear cohesion.

Work **branch by branch** (domain at a time), not random file thrash. Keep the tree buildable as often as practical.

---

## Phase 5 — Document every class, type, and structure

After or during the rewrite, **every** retained type gets rich in-code documentation using the language’s native system ([DOC-COMMENTS.md](DOC-COMMENTS.md)):

| Language family | System |
|-----------------|--------|
| Java / Kotlin | Javadoc / KDoc |
| Python | docstrings (PEP 257) + types |
| Rust | rustdoc (`///`, `//!`) |
| Go | Go doc comments |
| C# / F# | XML doc comments |
| TypeScript / JS | TSDoc / JSDoc |
| Swift | DocC `///` |
| C / C++ | Doxygen-style or project standard |
| Other | That ecosystem’s idiomatic doc generator syntax |

Each type doc should cover: **what it is**, **why it exists**, **invariants**, **important fields/variants**, **how it relates to neighboring types** (graph neighbors). Use rich markup the tool supports (lists, code, links to other types, examples).

Undocumented public/internal domain types = unfinished unslop.

---

## Phase 6 — Verify

1. Build / typecheck succeeds.
2. Existing tests pass (or update tests only when structure requires it — say why).
3. Spot-check: a newcomer can open the root and navigate the tree without tribal knowledge.
4. Inventory updated if types were renamed/split.

---

## Hard rules

- **Understand first** — no large rewrite before understanding + inventory + approved plan.
- **No hallucinated APIs** — only types and paths that exist (or that you are introducing deliberately in the plan).
- **Clarity over cleverness** — delete slop (dead code, nonsense indirection, copy-paste towers); keep useful abstraction.
- **Do not strip features** to “simplify” unless the user asks.
- **Docs live next to code** — not only in a separate wiki (extra ARCH notes OK as complement).
- Match the **user’s project** conventions when they conflict with generic examples above.

## Anti-slop targets (remove or reshape)

- God classes / kitchen-sink modules
- Circular imports
- Duplicate types for the same concept
- Meaningless names (`Data2`, `HelperUtilManager`)
- Hidden side effects in “simple” getters
- Comment noise that restates the code instead of intent/invariants
