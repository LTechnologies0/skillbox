# In-code documentation (rich native syntax)

Document **every** class, type, and data structure that remains after unslop. Prefer the language’s own doc generator so docs ship with the code.

## Universal content (all languages)

Each type’s doc block should include:

1. **Summary** — one sentence: what it is.
2. **Responsibility** — why it exists in the module graph (which branch).
3. **Invariants** — what must always be true.
4. **Fields / variants** — meaningful members (skip trivial getters unless they matter).
5. **Relationships** — links/names of neighboring types (parent ports, children values).
6. **Example** — short usage when non-obvious (rich markup / fenced code where supported).

Also document **modules/packages** with a file-level or module-level doc (`//!`, package doc, `__init__` docstring, etc.) describing the branch.

Do **not** write comments that only repeat the identifier (`// the user` on `user`).

---

## Language cheatsheets

### Java (Javadoc)

```java
/**
 * Invoice issued to a customer after checkout.
 *
 * <p>Belongs to the {@code billing} branch. Immutable once {@link #finalize()} runs.
 *
 * <h2>Invariants</h2>
 * <ul>
 *   <li>{@code lineItems} is non-empty</li>
 *   <li>{@code total} equals the sum of line items</li>
 * </ul>
 *
 * @param customerId stable customer key
 * @see LineItem
 */
public record Invoice(CustomerId customerId, List<LineItem> lineItems, Money total) { }
```

### Kotlin (KDoc)

```kotlin
/**
 * Invoice issued after checkout.
 *
 * Lives in the billing branch. Prefer [InvoiceFactory] for construction.
 *
 * ## Invariants
 * - [lineItems] is not empty
 * - [total] matches the sum of line items
 *
 * @property customerId stable customer key
 * @see LineItem
 */
data class Invoice(
    val customerId: CustomerId,
    val lineItems: List<LineItem>,
    val total: Money,
)
```

### Python (docstrings)

```python
@dataclass(frozen=True)
class Invoice:
    """Invoice issued after checkout.

    Billing-branch value object. Construct via ``Invoice.issue(...)``.

    Invariants:
        * ``line_items`` is non-empty
        * ``total`` equals the sum of line items

    Args:
        customer_id: Stable customer key.
        line_items: Purchased lines.
        total: Aggregate money amount.

    Example:
        >>> inv = Invoice.issue(customer_id, lines)
    """

    customer_id: CustomerId
    line_items: tuple[LineItem, ...]
    total: Money
```

### Rust (rustdoc)

```rust
/// Invoice issued after checkout.
///
/// Billing-branch value object. Use [`Invoice::issue`] to construct.
///
/// # Invariants
/// - `line_items` is non-empty
/// - `total` equals the sum of line items
///
/// # Examples
/// ```
/// let inv = Invoice::issue(customer_id, lines)?;
/// ```
pub struct Invoice {
    pub customer_id: CustomerId,
    pub line_items: Vec<LineItem>,
    pub total: Money,
}
```

Module docs: `//!` at the top of `billing/mod.rs` describing the branch.

### Go

```go
// Invoice is issued after checkout.
//
// Billing-branch value object. Construct with [NewInvoice].
//
// Invariants:
//   - len(LineItems) > 0
//   - Total equals the sum of line items
type Invoice struct {
	CustomerID CustomerID
	LineItems  []LineItem
	Total      Money
}
```

### C# (XML docs)

```csharp
/// <summary>
/// Invoice issued after checkout.
/// </summary>
/// <remarks>
/// <para>Billing branch value object.</para>
/// <para><b>Invariants:</b> line items non-empty; total equals sum.</para>
/// </remarks>
/// <seealso cref="LineItem"/>
public sealed record Invoice(CustomerId CustomerId, IReadOnlyList<LineItem> LineItems, Money Total);
```

### TypeScript / JavaScript (TSDoc / JSDoc)

```ts
/**
 * Invoice issued after checkout.
 *
 * Billing-branch value object. Use {@link issueInvoice} to construct.
 *
 * @remarks
 * Invariants: `lineItems` non-empty; `total` equals sum of lines.
 *
 * @see LineItem
 */
export type Invoice = {
  customerId: CustomerId;
  lineItems: readonly LineItem[];
  total: Money;
};
```

### Swift (DocC)

```swift
/// Invoice issued after checkout.
///
/// Billing-branch value object.
///
/// ## Invariants
/// - `lineItems` is non-empty
/// - `total` equals the sum of line items
public struct Invoice {
    public let customerId: CustomerId
    public let lineItems: [LineItem]
    public let total: Money
}
```

### C / C++ (Doxygen-style — match project)

```cpp
/**
 * @brief Invoice issued after checkout.
 *
 * @details Billing-branch value. Invariants: non-empty lines; total == sum.
 * @see LineItem
 */
struct Invoice {
  CustomerId customer_id;
  std::vector<LineItem> line_items;
  Money total;
};
```

---

## Coverage rule

| Kind | Required doc |
|------|----------------|
| Public API types | Full rich doc + examples when non-obvious |
| Internal domain types | Full rich doc (summary + invariants + relationships) |
| Private helpers | Short summary if non-obvious; skip noise on trivial locals |
| Modules / packages | Branch purpose + allowed dependents |

Unslop is incomplete while inventory types lack these docs.

## Generation tips

- Link to sibling types with the language’s link syntax (`{@link}`, `[Type]`, `[Type]`, `cref`, …).
- Prefer documenting **ports/interfaces** with “implemented by / called by” relationships.
- Keep examples compiling when the doc tool runs doctests (Rust, Python).
