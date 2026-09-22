# Graph / tree / branch modular writing style

Unslop means the **shape of the code** teaches the reader. Prefer a navigable tree and an intentional dependency graph over a flat pile of files.

## Metaphors (use them when explaining plans)

| Metaphor | Meaning in code |
|----------|-----------------|
| **Tree** | Folder/module hierarchy: root → domains → leaves. Depth is purposeful, not accidental. |
| **Branch** | One cohesive subtree (e.g. `billing/`, `auth/`) with its types and use-cases together. |
| **Graph** | Import/dependency edges between modules. Edges should be few, named, and preferably acyclic. |
| **Modular** | Each unit has a boundary, a clear API, and replaceable internals. |
| **Structured** | Predictable layout: same patterns for errors, results, constructors, and docs everywhere. |

## Layout principles

1. **Root is the map** — package/`lib`/`src` entry shows branches; readers should not need a tour guide.
2. **Branch = domain or adapter** — mix fewer concerns per folder.
3. **Leaves are small and boring** — pure functions, DTOs, single-purpose types.
4. **Shared is scarce** — `shared/`/`common/` only for true cross-cutting primitives; do not dump leftovers there.
5. **Dependencies point one way** — adapters depend on domain/application; domain does not import UI/DB frameworks (unless the project already chose a different core — then follow *that* graph).

## Type-level structure

- One primary concept per type name.
- Prefer explicit data structures (records/structs/enums) over anonymous bags/`Any`/`Object`.
- Encode illegal states out of existence when cheap (enums, newtypes, sealed hierarchies).
- Constructors/factories validate invariants; document those invariants in the type doc.
- Side effects live at the edges (I/O adapters), not deep in domain leaves.

## Naming

- Names mirror the tree: `billing.Invoice` not `DataHelper`.
- Verbs for actions/use-cases; nouns for entities/values.
- Rename when the inventory shows two names for one concept.

## File / module size heuristic

- Split when unrelated domains share a file, or when navigation requires scrolling past unrelated concepts.
- Merge when tiny files only exist to re-export noise or break an obvious cohesion.
- Do not fetishize “one type per file” or “everything in one file” — optimize for **reader navigation**.

## Import hygiene

- No circular imports; break cycles with interfaces, events, or moving shared types to a lower leaf.
- Prefer importing from stable branch façades over deep reaching into another branch’s internals (when the language supports visibility).
- After moves: update all import sites from the inventory; leave no stale paths.

## What “rewritten style” looks like in a PR

- Folder tree matches the approved plan.
- Types sit next to their branch logic.
- Cross-branch wiring is visible (composition root / DI module / `main`).
- Dead duplicates and unused imports are gone.
- Every type has rich docs ([DOC-COMMENTS.md](DOC-COMMENTS.md)).

## Anti-patterns to eliminate

| Slop | Structured fix |
|------|----------------|
| Everything under `utils/` | Move to owning branch; keep only true generics in shared |
| Circular `A↔B` imports | Extract shared type or introduce port interface |
| 2k-line “Manager” | Split by use-case / entity |
| Copy-pasted DTOs | One canonical type + mappers at edges |
| Comments that narrate syntax | Replace with invariant/graph docs on the type |
