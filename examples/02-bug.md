# Example 02 — Bug

## Input (Jira ticket `SHOP-812`, summarized)

> Customers say the cart total is wrong when they apply a discount code and then change quantity. Happened on web, prod. Screenshot shows total not updating after quantity change.

---

## `/analyze` output

## Spec — Cart total not recalculated after quantity change with discount
Source: SHOP-812 · Type: Bug

### Summary
With a discount code applied, changing an item's quantity in the cart does not update the total, so customers see (and may be charged) an incorrect amount.

### Steps to reproduce
1. Add any item to the cart.
2. Apply a valid percentage discount code.
3. Change the item quantity from 1 to 2.

### Expected
Subtotal, discount, and total update immediately to reflect quantity 2.

### Actual
Subtotal updates; discount and total keep the quantity-1 values.

### Environment
Web, production. Browser not specified.

### Requirements
- FR-001: WHEN the cart contents change THE SYSTEM SHALL recalculate the discount and total using the current contents.
- FR-002: THE SYSTEM SHALL charge the amount shown as total at checkout.

### Assumptions
- A1: Only percentage discounts are affected (not verified for fixed-amount codes).

### Open questions
- [NEEDS CLARIFICATION: Has any order been charged an incorrect amount? (FR-002) — affects severity and whether a data fix is needed.]

### Risks & dependencies
- If checkout uses the cached total, customers may have been charged incorrectly.

### Suggested task type
Bug — existing behavior is incorrect.

→ Next: `/clarify`

---

## `/breakdown` output (after clarification: "checkout recalculates server-side, no wrong charges")

| Ref | Type | Title | Priority | SP | Hours | Parallel | Covers | Blocked by |
|---|---|---|---|---|---|---|---|---|
| B1 | Bug | [Cart] Total not updated when quantity changes with discount applied | P1 | 2 | 5 | | FR-001, FR-002 | – |
| B1.1 | Sub-task | FE: Recalculate discount on cart quantity change | | | 3 | | | – |
| B1.2 | Sub-task | QA: Add regression test for discount + quantity change | | | 2 | | | B1.1 |

**Severity:** Major (display only; server-side checkout is correct)

### B1 acceptance criteria
- AC1: Given a cart with a 10% discount applied, when I change quantity from 1 to 2, then discount and total update without a page reload.
- AC2: Given the same cart, when I proceed to checkout, then the checkout total equals the cart total.
- AC3: A regression test covers percentage and fixed-amount codes.

---
Covers: FR-001, FR-002
Source: SHOP-812
