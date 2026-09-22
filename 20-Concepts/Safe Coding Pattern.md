---
tags: [concept, safety]
---

# Safe Coding Pattern

Shift safety from every call site to **safe abstractions**: risky operations (memory, injection sinks, FFI) live in small audited modules with APIs that cannot be misused into UB/vulns; the typechecker/linter enforces the rest.

See [[10-Skills/Safe Codebase/Index]] and [[skills/safe-codebase/SAFETY-CATALOG]].
