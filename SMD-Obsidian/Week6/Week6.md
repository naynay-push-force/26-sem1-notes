# Key Concepts Checklist

-  [ ] 1. UML State Machine Diagrams — Overview
	-  [ ] 1.1 Definition and purpose
	-  [ ] 1.2 Role in the UP model landscape
	-  [ ] 1.3 Core components: state, event, transition
-  [ ] 2. Notation
	-  [ ] 2.1 Basic diagram elements
	-  [ ] 2.2 Transition actions
	-  [ ] 2.3 Guards
-  [ ] 3. When to Apply State Machines
	-  [ ] 3.1 State-dependent vs state-independent objects
	-  [ ] 3.2 Guideline 1: complex state-dependent objects
	-  [ ] 3.3 Guideline 2: domain applicability
-  [ ] 4. Nested States
	-  [ ] 4.1 Definition and inheritance of transitions
	-  [ ] 4.2 Telephone example
-  [ ] 5. Choice Pseudostate
	-  [ ] 5.1 Definition and purpose
	-  [ ] 5.2 Two or more outgoing transitions
	-  [ ] 5.3 `[else]` guard

---

# 1. UML State Machine Diagrams — Overview

## 1.1 Definition and Purpose

**State machine** — a behaviour model that captures the dynamic behaviour of an object in terms of states, events, and state transitions

- Models how an object reacts differently based on its current status or condition
- Answers: _"If an object can have different behaviour based on its status or condition, how can we model it?"_

## 1.2 Role in the UP Model Landscape

> [!info] Image: Four quadrant diagram showing: static domain model (domain class diagram), dynamic domain model (SSD), static design model (DCD), dynamic design model (design sequence diagram). State machine diagrams complement these by modelling object-level dynamic behaviour. Slide 3.

![[Pasted image 20260616152953.png]]
## 1.3 Core Components

| Component | Definition |
|---|---|
| **State** | Condition of an object at a moment in time |
| **Event** | A significant or noteworthy occurrence that causes the object to change state |
| **Transition** | A directed relationship between two states such that an event can cause the object to move from the prior state to the subsequent state |

> [!example] Telephone State Machine
> States: `Idle`, `Active` — Event: `Pick up receiver` — Transition: `Idle → Active`
> Reverse: event `Hang up / on hook` — Transition: `Active → Idle`

> [!info] Image: Simple UML state machine diagram for a telephone — filled circle (initial pseudostate) → `Idle` state → `Active` state. Transition labelled "Pick up receiver"; return transition labelled "Hang up / on hook". Slide 4.

![[Pasted image 20260616160054.png]]

> [!summary] Key takeaways
> - State machines model dynamic, state-dependent object behaviour
> - Three building blocks: state, event, transition
> - Represented visually as UML State Machine Diagrams

---

# 2. Notation

## 2.1 Basic Diagram Elements

- **Initial pseudostate** — filled circle; indicates starting state
- **State** — rounded rectangle with state name
- **Transition** — directed arrow between states, labelled with trigger event

> [!info] Image: Partial state machine diagram for an iPhone showing multiple states and transitions triggered by the side button. Slide 6.

![[Pasted image 20260616204640.png]]
## 2.2 Transition Actions

**Transition action** — an action performed when a transition fires
- In software implementation, may represent invocation of a method on the class

## 2.3 Guards

**Guard** — a pre-condition on a transition; the transition will not fire if the guard is false

**Syntax:**
```
trigger [ guard ] / action
```

When the object is in `State A`:
- if `trigger` event occurs **and** `guard` is true
- then perform `action` and transition to `State B`

> [!info] Image: Notation diagram showing `State A` → `State B` with label `trigger [ guard ] / action`; left annotation "states", bottom annotation "transition action", right annotation "guard", middle annotation "transition". Slide 11.

![[Pasted image 20260616204727.png]]

> [!example] Photography Website
> Transition with guard — e.g. `Upload photo [file size < 10MB] / compress and store`. Slide 12.

> [!example] Pedestrian Crossing Light (full diagram)
> - Initial state: `Red Standing`
> - `button pressed [Traffic Light is Red] / Timer starts` → `Green Walking`
> - `Timer reaches 30 seconds` → `Flashing Red Standing`
> - `Timer reaches 60 seconds / Reset Timer` → `Red Standing`
>
> Guard `[Traffic Light is Red]` prevents the crossing light changing if cars still have green. Slide 17.

> [!info] Image: Full pedestrian crossing state machine — three states: `Red Standing` (initial), `Green Walking`, `Flashing Red Standing`. Transitions as described above with guard and action labels. Slide 17.

![[Pasted image 20260616205001.png]]

> [!summary] Key takeaways
> - Syntax: `trigger [ guard ] / action` on transition arrow
> - Guard is a boolean pre-condition — transition blocked if false
> - Action is triggered on the transition itself, not inside a state

---

# 3. When to Apply State Machines

## 3.1 State-Dependent vs State-Independent Objects

| Object type                           | Description                                                 |
| ------------------------------------- | ----------------------------------------------------------- |
| **State-dependent**                   | Reacts differently to events depending on current state     |
| **State-independent**                 | Always reacts to an event the same way, regardless of state |
| **State-independent w.r.t. an event** | Always responds to a specific event the same way            |

- State machines are only warranted for **state-dependent** objects

## 3.2 Guideline 1: Complex State-Dependent Objects

- Consider state machines for state-dependent objects with **complex behaviour**
- Model behaviour of complex reactive objects

## 3.3 Guideline 2: Domain Applicability

| More common in | Less common in |
|---|---|
| Communications and control applications | Business information systems |
| Physical device controllers | — |
| UI page/window flow, navigation, session | — |
| Protocols and legal sequences | — |
| Use case operation sequencing | — |
| Transactions and role mutators | — |

> [!example] Process Sale Operation Sequencing
> States: `WaitingForSale`, `EnteringItems`, `WaitingForPayment`, `AuthorizingPayment`
> Transitions: `makeNewSale`, `enterItem`, `endSale`, `makeCashPayment`, `makeCreditPayment`, `makeStoreCheckPayment`, `authorized`. Slide 10.

> [!info] Image: State machine diagram for the Process Sale use case showing four states and all transitions labelled with operation names. Slide 10.

![[Pasted image 20260616151518.png]]

> [!info] Image: State machine diagram for web page navigation showing page states and navigation event transitions. Slide 9.

![[Pasted image 20260616151605.png]]

> [!summary] Key takeaways
> - Only model state-dependent objects with complex behaviour
> - More applicable in control/communications domains than typical business systems
> - Use case operation sequencing is a common application

---

# 4. Nested States

## 4.1 Definition and Inheritance

**Nested state** — a state contains substates; the enclosing state is the **superstate**
- A substate **inherits** all transitions defined on its superstate
- Transition into a superstate enters the designated initial substate

## 4.2 Telephone Example

- Superstate: `Active` — contains substates `PlayingDialTone`, `Dialing`, `Connecting`, `Talking`
- Transition `Pick up / play dial tone` → enters `Active`, initially landing in `PlayingDialTone`
- Transition `on hook` (hang up) is defined on `Active` — **all substates inherit it**
- Internal substate transitions: `digit` (`PlayingDialTone → Dialing → Connecting`), `connected` (`Connecting → Talking`), `tr complete` (`Dialing → Connecting`)

> [!info] Image: Telephone nested state machine — outer states `Idle` and `Active (superstate)`; `Active` contains substates `PlayingDialTone`, `Dialing`, `Connecting`, `Talking` with internal transitions. `on hook` transition on `Active` is inherited by all substates. Slide 18.

![[Pasted image 20260616212410.png]]

> [!note] Substates inherit transitions from their superstate. If a substate defines its own transition for the same event, it overrides the superstate's transition for that event.

> [!summary] Key takeaways
> - Nesting reduces diagram clutter by hoisting shared transitions to the superstate
> - Substate inherits all superstate transitions
> - Entry into superstate defaults to its initial substate

---

# 5. Choice Pseudostate

## 5.1 Definition and Purpose

**Choice pseudostate** — realizes a **dynamic conditional branch**; evaluates guards of outgoing transitions at runtime to select exactly one outgoing path

- Depicted as a diamond shape
- Guards are evaluated when the pseudostate is entered; exactly one transition is taken

## 5.2 Two or More Outgoing Transitions

- A choice pseudostate can have two or more outgoing transitions, each with a different guard
- Example: ATM PIN entry
  - `Entered PIN [PIN Correct] / Display Menu` → `Selecting Transaction`
  - `Entered PIN [PIN Incorrect] / Stun Patron` → `Calling Police`

> [!info] Image: Two equivalent representations of an ATM PIN check — left uses a choice pseudostate diamond between `Entering PIN` and the two outcomes; right merges the guard directly onto transition arrows from `Entering PIN`. Both are valid. Slide 19.

![[Pasted image 20260616212646.png]]

> [!info] Image: Three-way choice pseudostate — `State A` → diamond → three outgoing transitions guarded `[bal < 0]` → `State A`, `[0 <= bal < min]` → `State B`, `[min <= bal]` → `State C`. Slide 20.

![[Pasted image 20260616212820.png]]
## 5.3 `[else]` Guard

- Predefined `[else]` guard — taken if no other outgoing guard is true
- Ensures exactly one transition always fires

> [!example] Day-based branch
> `trigger [day = Monday]` → `State A`; `trigger [else]` → `State B`. Slide 20.

> [!note] Guards on a choice pseudostate must be mutually exclusive and collectively exhaustive (or use `[else]`). If multiple guards could be true simultaneously, the model is ill-formed.

> [!summary] Key takeaways
> - Choice pseudostate = dynamic conditional branch (diamond)
> - All outgoing guards evaluated at runtime; exactly one fires
> - `[else]` guard acts as a catch-all default
> - Shorthand: guards can be placed directly on transition arrows without an explicit diamond
