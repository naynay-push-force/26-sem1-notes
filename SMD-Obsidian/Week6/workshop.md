# Workshop 6 — State Machines: Pre-read Guide

> [!note] This is a pre-read to walk into workshop already knowing the answers. Q1.1 and Q1.2 are diagrams — draw them in draw.io or on paper. Q2.1 and Q2.2 are code — clone the GitHub repo first.

---

# Q1.1 — State Machine for a Credit Card Account

## States

Extract these directly from the description — one per distinct "status" the account can be in:

| State | When |
|---|---|
| `Pending` | Application approved + card issued but not yet activated; also when lost/stolen reported |
| `Active` | Card activated; normal operating state |
| `Suspended` | Credit limit exceeded |
| `Default` | Failed to pay bill on time |
| `Inactive` | No outstanding bills + card unused for 6+ months |
| `Closed` | Account closed (from Default or Inactive) |

## Transitions

| From | Event / Guard | To |
|---|---|---|
| *(initial)* | application approved, card issued | `Pending` |
| `Pending` | customer activates card | `Active` |
| `Active` | card reported lost or stolen / new card issued | `Pending` |
| `Active` | credit usage exceeds limit | `Suspended` |
| `Suspended` | [available funds > 0] | `Active` |
| `Active` | fails to pay bill on time | `Default` |
| `Default` | pays all outstanding bills | `Active` |
| `Default` | does not respond appropriately | `Closed` |
| `Active` | [no outstanding bills] card not used for 6 months *(temporal)* | `Inactive` |
| `Inactive` | customer contacts company to resume | `Active` |
| `Inactive` | inactive for more than 6 months *(temporal)* | `Closed` |
| `Closed` | submits new application (approved) | `Pending` |

> [!info] Image: Draw this as a state machine diagram. Initial pseudostate → `Pending`. `Active` is the central hub with the most outgoing transitions. `Closed` has one outgoing arrow back to `Pending`. `Suspended` has a self-loop via `Active` with a guard. `Default` will get nested substates in Q1.2.

## Event Types (Discussion Question)

| Event | Type | Why |
|---|---|---|
| Customer activates card | External | Triggered by user action |
| Card reported lost/stolen | External | Triggered by user |
| Pays all outstanding bills | External | Triggered by user |
| Contacts company to resume | External | Triggered by user |
| Submits new application | External | Triggered by user |
| Credit limit exceeded | Internal | System checks balance |
| Available funds > 0 | Internal | System checks balance |
| Card not used for 6 months | Temporal | System clock check |
| Inactive for 6+ months | Temporal | System clock check |

---

# Q1.2 — Nested States within `Default`

## Substates of `Default`

| Substate | When entered |
|---|---|
| `GracePeriod` | First missed payment, no history of missed payments |
| `PaymentPlanOffered` | Has history of missed payments, OR failed to pay within grace period (+ $20 fee charged) |
| `HealthyDebt` | Accepted the payment plan |
| `UnhealthyDebt` | Refused / no response to payment plan; OR `HealthyDebt` misses a payment |
| `Collections` | Referred to third-party collections (from `UnhealthyDebt`, eventually / temporal) |

## Transitions within `Default`

| From | Event / Guard | To |
|---|---|---|
| *(entry into Default)* | [no history of missed payments] | `GracePeriod` |
| *(entry into Default)* | [has history of missed payments] | `PaymentPlanOffered` |
| `GracePeriod` | fails to pay within grace period / charge $20 fee | `PaymentPlanOffered` |
| `PaymentPlanOffered` | accepts payment plan | `HealthyDebt` |
| `PaymentPlanOffered` | refuses OR no response within reasonable time | `UnhealthyDebt` |
| `HealthyDebt` | misses a payment | `UnhealthyDebt` |
| `UnhealthyDebt` | eventually *(temporal)* | `Collections` |
| `Collections` | debt written off or collected | `Closed` *(exits Default)* |

## Shared transition on the `Default` superstate

> [!note] Key modelling decision here.

"If a customer pays their full overdue bill **at any point prior to being referred to collections**, the account becomes Active."

This means `GracePeriod`, `PaymentPlanOffered`, `HealthyDebt`, and `UnhealthyDebt` all share the transition:
- `pays full overdue bill` → `Active` *(exits Default)*

Put this on the **`Default` superstate** — all four substates inherit it. `Collections` then overrides it by having its own outgoing transition (to `Closed`) which takes priority, so the "pay to become active" path is no longer available once in `Collections`.

> [!info] Image: Draw `Default` as a rounded rectangle (superstate) containing the five substates. Use a choice pseudostate (diamond) at the entry point of `Default` to branch between `GracePeriod` (no history) and `PaymentPlanOffered` (has history). The `pays full overdue bill → Active` transition arrow sits on the boundary of the `Default` superstate box — inherited by all substates except overridden by `Collections`.

---

# Q2.1 — Design Class Diagram

## Classes

**`Account`**
- `- state: AccountState`
- `- defaultSubState: DefaultSubState` *(only relevant when state == DEFAULT)*
- `+ activate(): void`
- `+ reportLostOrStolen(): void`
- `+ exceedCreditLimit(): void`
- `+ fundsReplenished(): void` *(guard: available funds > 0)*
- `+ missPayment(hasHistory: boolean): void`
- `+ payOutstandingBills(): void`
- `+ doesNotRespond(): void`
- `+ contactToResume(): void`
- `+ cardUnusedSixMonths(): void`
- `+ inactiveOverSixMonths(): void`
- `+ submitNewApplication(): void`
- `+ acceptPaymentPlan(): void`
- `+ refusePaymentPlan(): void`
- `+ referToCollections(): void`
- `+ debtResolved(): void`

**`AccountState`** *(enum)*
- `PENDING, ACTIVE, SUSPENDED, DEFAULT, INACTIVE, CLOSED`

**`DefaultSubState`** *(enum)*
- `GRACE_PERIOD, PAYMENT_PLAN_OFFERED, HEALTHY_DEBT, UNHEALTHY_DEBT, COLLECTIONS`

> [!note] The workshop provides an interface on GitHub — your `Account` class should implement it. Check the interface method names before finalising yours.

---

# Q2.2 — Java Implementation

## Pattern to use

Enum-based state machine — simplest approach for this workshop. Each method checks the current state and throws an exception or ignores if the transition is invalid.

## Skeleton

```java
public enum AccountState {
    PENDING, ACTIVE, SUSPENDED, DEFAULT, INACTIVE, CLOSED
}

public enum DefaultSubState {
    GRACE_PERIOD, PAYMENT_PLAN_OFFERED, HEALTHY_DEBT, UNHEALTHY_DEBT, COLLECTIONS
}

public class Account {
    private AccountState state;
    private DefaultSubState defaultSubState;

    public Account() {
        this.state = AccountState.PENDING; // initial state
    }

    public void activate() {
        if (state == AccountState.PENDING) {
            state = AccountState.ACTIVE;
        } else {
            throw new IllegalStateException("Cannot activate from " + state);
        }
    }

    public void reportLostOrStolen() {
        if (state == AccountState.ACTIVE) {
            state = AccountState.PENDING; // new card issued
        } else {
            throw new IllegalStateException("Cannot report lost/stolen from " + state);
        }
    }

    public void exceedCreditLimit() {
        if (state == AccountState.ACTIVE) {
            state = AccountState.SUSPENDED;
        } else {
            throw new IllegalStateException("Cannot suspend from " + state);
        }
    }

    public void fundsReplenished() {
        // guard: available funds > 0 (assumed true if this method is called)
        if (state == AccountState.SUSPENDED) {
            state = AccountState.ACTIVE;
        } else {
            throw new IllegalStateException("Cannot resume from " + state);
        }
    }

    public void missPayment(boolean hasHistory) {
        if (state == AccountState.ACTIVE) {
            state = AccountState.DEFAULT;
            defaultSubState = hasHistory
                ? DefaultSubState.PAYMENT_PLAN_OFFERED
                : DefaultSubState.GRACE_PERIOD;
        } else if (state == AccountState.DEFAULT
                   && defaultSubState == DefaultSubState.HEALTHY_DEBT) {
            defaultSubState = DefaultSubState.UNHEALTHY_DEBT;
        } else {
            throw new IllegalStateException("Cannot miss payment from " + state + "/" + defaultSubState);
        }
    }

    public void failGracePeriod() {
        if (state == AccountState.DEFAULT
                && defaultSubState == DefaultSubState.GRACE_PERIOD) {
            defaultSubState = DefaultSubState.PAYMENT_PLAN_OFFERED;
            // action: charge $20 fee
        } else {
            throw new IllegalStateException("Not in grace period");
        }
    }

    public void acceptPaymentPlan() {
        if (state == AccountState.DEFAULT
                && defaultSubState == DefaultSubState.PAYMENT_PLAN_OFFERED) {
            defaultSubState = DefaultSubState.HEALTHY_DEBT;
        } else {
            throw new IllegalStateException("No payment plan to accept");
        }
    }

    public void refusePaymentPlan() {
        if (state == AccountState.DEFAULT
                && defaultSubState == DefaultSubState.PAYMENT_PLAN_OFFERED) {
            defaultSubState = DefaultSubState.UNHEALTHY_DEBT;
        } else {
            throw new IllegalStateException("No payment plan to refuse");
        }
    }

    public void referToCollections() {
        if (state == AccountState.DEFAULT
                && defaultSubState == DefaultSubState.UNHEALTHY_DEBT) {
            defaultSubState = DefaultSubState.COLLECTIONS;
        } else {
            throw new IllegalStateException("Cannot refer to collections");
        }
    }

    public void payOutstandingBills() {
        // Valid from any Default substate EXCEPT Collections
        if (state == AccountState.DEFAULT
                && defaultSubState != DefaultSubState.COLLECTIONS) {
            state = AccountState.ACTIVE;
            defaultSubState = null;
        } else {
            throw new IllegalStateException("Cannot pay bills to become active from " + defaultSubState);
        }
    }

    public void debtResolved() {
        // Collections: debt written off or collected → Closed
        if (state == AccountState.DEFAULT
                && defaultSubState == DefaultSubState.COLLECTIONS) {
            state = AccountState.CLOSED;
            defaultSubState = null;
        } else {
            throw new IllegalStateException("Not in collections");
        }
    }

    public void doesNotRespond() {
        // Direct Default → Closed (does not respond at all)
        if (state == AccountState.DEFAULT) {
            state = AccountState.CLOSED;
            defaultSubState = null;
        } else {
            throw new IllegalStateException("Cannot close from " + state);
        }
    }

    public void cardUnusedSixMonths() {
        // guard: no outstanding bills (assumed true)
        if (state == AccountState.ACTIVE) {
            state = AccountState.INACTIVE;
        } else {
            throw new IllegalStateException("Cannot go inactive from " + state);
        }
    }

    public void contactToResume() {
        if (state == AccountState.INACTIVE) {
            state = AccountState.ACTIVE;
        } else {
            throw new IllegalStateException("Cannot resume from " + state);
        }
    }

    public void inactiveOverSixMonths() {
        if (state == AccountState.INACTIVE) {
            state = AccountState.CLOSED;
        } else {
            throw new IllegalStateException("Not inactive");
        }
    }

    public void submitNewApplication() {
        if (state == AccountState.CLOSED) {
            state = AccountState.PENDING;
        } else {
            throw new IllegalStateException("Account is not closed");
        }
    }

    public AccountState getState() { return state; }
    public DefaultSubState getDefaultSubState() { return defaultSubState; }
}
```

## Demo `main` method

```java
public static void main(String[] args) {
    Account acc = new Account();
    System.out.println(acc.getState()); // PENDING

    // Normal flow
    acc.activate();
    System.out.println(acc.getState()); // ACTIVE

    // Suspend and recover
    acc.exceedCreditLimit();
    System.out.println(acc.getState()); // SUSPENDED
    acc.fundsReplenished();
    System.out.println(acc.getState()); // ACTIVE

    // Default with nested states
    acc.missPayment(false); // no history → GracePeriod
    System.out.println(acc.getDefaultSubState()); // GRACE_PERIOD
    acc.failGracePeriod();
    System.out.println(acc.getDefaultSubState()); // PAYMENT_PLAN_OFFERED
    acc.acceptPaymentPlan();
    System.out.println(acc.getDefaultSubState()); // HEALTHY_DEBT
    acc.missPayment(false); // misses again → UnhealthyDebt
    System.out.println(acc.getDefaultSubState()); // UNHEALTHY_DEBT
    acc.referToCollections();
    System.out.println(acc.getDefaultSubState()); // COLLECTIONS
    acc.debtResolved();
    System.out.println(acc.getState()); // CLOSED

    // New application
    acc.submitNewApplication();
    System.out.println(acc.getState()); // PENDING

    // Guard demo: invalid transition
    try {
        acc.exceedCreditLimit(); // PENDING → can't suspend
    } catch (IllegalStateException e) {
        System.out.println("Caught: " + e.getMessage()); // shows guard blocking invalid transition
    }
}
```

> [!note] The guard demo at the end is important — the tutor will ask you to show that invalid transitions are blocked. The `IllegalStateException` is your guard in action.
