# Full inventory protocol

Complete this inventory **before** any large rewrite. Scope = whole repo or the path the user specified.

## 1. Enumerate type-like entities

Search by language idioms (adapt):

- Classes / objects / companions
- Structs / records / data classes
- Enums / sealed types / unions
- Interfaces / traits / protocols / typeclasses
- Type aliases / newtypes / typedefs
- Generics / templates (note parameters and constraints)

For each entry record:

```text
Name:
Kind:
Path:
Visibility: (public / internal / private)
Fields | Variants | Methods (summary):
Type params / bounds:
Supertypes / implements:
Defined next to: (module / package)
```

## 2. Map import / usage graph

For each type, find **all** references:

- `import` / `use` / `include` / `using` / `#include`
- Same-package references without import
- Reflection / DI registration / service locators (note as “dynamic use”)
- Re-exports / barrel files / `mod` declarations

Record:

```text
Type → used by: [paths…]
Type → depends on: [types…]
```

Flag:

- **Hubs** (many dependents) — move carefully; document heavily
- **Orphans** (zero use) — candidates for deletion after confirmation
- **Cycles** — must break in the plan

## 3. Classify role in the target tree

Assign each type a provisional role:

| Role | Meaning |
|------|---------|
| Root / façade | App entry or public library surface |
| Domain entity / value | Core business meaning |
| Domain service | Pure rules over entities |
| Application use-case | Orchestrates domain + ports |
| Port / interface | Boundary contract |
| Adapter | UI, DB, HTTP, file, vendor SDK |
| Shared primitive | Truly cross-cutting |
| Test double / fixture | Test-only |

## 4. Control-flow & entry points

List:

- Program / server / CLI / worker entry points
- Public API surface (if a library)
- Important branches: auth, persistence, rendering, etc.

Trace **critical paths** depth-first: entry → callees → types touched. Hold that chain in the plan before editing.

## 5. Deliverables to the user

1. **Understanding summary** (1 screen): what the system does, domains, risks.
2. **Inventory** (table or grouped lists): all types + paths.
3. **Import graph notes**: hubs, orphans, cycles.
4. **Proposed module tree** with where each hub type will live.
5. **Explicit ask**: “Approve this plan before rewrite?”

## 6. During rewrite

Keep the inventory alive:

- Rename/split → update the list
- Delete only with evidence (orphan + user OK, or replaced by named successor)
- New types get inventory rows **and** docs immediately

## Verification statement (required before coding)

Before the first structural edit, state:

> Inventory covers the agreed scope. Entry points and critical flows are identified. No invented types. Awaiting approval of the module graph plan.
