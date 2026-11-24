# 🧪 Test Case Design & Professional Unit Testing Standards

Standard operating procedure for `kio-qa-security` governing automated Test Case Matrix generation and robust Unit Test authoring, completely eliminating regression bugs and AI code hallucinations.

---

## 1. 4-Quadrant Test Case Matrix

Before writing any unit test code, `agent-qa` must formulate a structured Test Case Matrix:

| Test ID | Category | Scenario Description | Preconditions | Input Payload | Expected Outcome | Severity |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| `TC-01` | **Happy Path** | Standard core execution flow | Valid data, system ready | Well-formed input data | Correct return value, Status 200/OK | `Critical` |
| `TC-02` | **Boundary Value** | Edge boundary analysis (0, min, max, n-1, n+1) | Near upper/lower limits | Exact boundary inputs (e.g. 0, empty string, 255 chars) | Graceful handling without overflow or crashes | `High` |
| `TC-03` | **Edge Case** | Malformed data, missing keys, unusual formats | Unstable network, corrupt payload | `null`, `undefined`, weird unicode strings, injection attempts | Clean business error thrown, no app crash | `High` |
| `TC-04` | **Error & Fallback**| Failure modes when dependencies crash | Downstream service timeout or 500 | Network error / DB offline | Safe fallback activated or retry triggered | `Medium` |
| `TC-05` | **Security / Auth** | Unauthorized access attempt, expired credentials | Unauthenticated / Invalid permissions | Request without JWT or impersonating another user | Blocked immediately with 401 Unauthorized / 403 Forbidden | `Critical` |

---

## 2. The Iron Law of Test-Driven Development (TDD)

AI tools frequently fall into the trap of writing implementation code first and then authoring tautological tests (circular tests that merely mirror whatever buggy code was generated). Kio strictly enforces:

1. **Red Phase (Failing Test First)**:
   - Based on specifications from `agent-pd`, author the test file first.
   - Execute the test runner and verify the test **FAILS (Exit code 1)** with the exact expected error.
2. **Green Phase (Minimal Implementation)**:
   - Write only the minimum amount of code required to turn the tests green (Exit code 0).
3. **Refactor Phase (Clean Architecture)**:
   - Refactor code structure, eliminate redundancy, and optimize performance while keeping all tests passing.

---

## 3. Disciplined Mocking Rules

- **NEVER Mock the Unit Under Test**: The module being tested must execute 100% real production code.
- **Mock Only External Side-Effects**:
  - Outbound HTTP requests to 3rd-party services (use `msw` or mock fetch).
  - Physical database connections (during unit testing; use SQLite in-memory or Testcontainers for integration tests).
  - System clocks and non-deterministic functions (`Date.now()`, `Math.random()`, `crypto.randomUUID()`).
- **Ban the "Mock-Everything Anti-Pattern"**: Tests where 90% of internal logic is mocked are completely worthless and provide zero confidence.

---

## 4. Multi-Language Unit Test Blueprints

### A. TypeScript / JavaScript (Vitest & React Testing Library)
```typescript
import { describe, it, expect, vi, beforeEach } from 'vitest';
import { calculateCartTotal, type CartItem } from './cartService';

describe('calculateCartTotal', () => {
  it('TC-01 [Happy Path]: accurately calculates total with valid items', () => {
    const items: CartItem[] = [
      { id: '1', price: 100, quantity: 2 },
      { id: '2', price: 50, quantity: 1 },
    ];
    const result = calculateCartTotal(items, { discountPercent: 10 });
    expect(result.subtotal).toBe(250);
    expect(result.total).toBe(225);
  });

  it('TC-02 [Boundary]: handles empty cart gracefully', () => {
    expect(calculateCartTotal([], { discountPercent: 0 }).total).toBe(0);
  });

  it('TC-03 [Edge Case]: throws an error when quantity or price is negative', () => {
    const invalidItems: CartItem[] = [{ id: '3', price: -50, quantity: 1 }];
    expect(() => calculateCartTotal(invalidItems)).toThrowError(/invalid value/i);
  });
});
```

### B. Python (Pytest & Parametrize)
```python
import pytest
from app.services.billing import calculate_discount, InvalidPromoException

@pytest.mark.parametrize("tier,cart_amount,expected_final", [
    ("bronze", 100.0, 100.0),    # 0% discount
    ("silver", 100.0, 90.0),     # 10% discount
    ("gold", 100.0, 80.0),       # 20% discount
])
def test_calculate_discount_tiers_happy_path(tier, cart_amount, expected_final):
    """TC-01: Correctly calculates discount tiers based on user tier."""
    final_price = calculate_discount(tier=tier, amount=cart_amount)
    assert final_price == expected_final

def test_invalid_negative_amount_raises_error():
    """TC-03: Blocks negative amount inputs."""
    with pytest.raises(ValueError, match="Amount cannot be negative"):
        calculate_discount(tier="bronze", amount=-10.0)
```

---

## 5. Verification Deliverables for Agent QA

Before passing tasks to Kio CTO, `agent-qa` must provide:
1. **The Formatted Test Case Matrix** covering Happy Path, Boundary, and Edge Cases.
2. **Terminal Execution Evidence** demonstrating:
   - Number of passing tests (e.g. `Tests: 14 passed, 14 total`).
   - Line coverage ratio (Code Coverage >= 80% on core business logic).
   - Exit code: `0`.
