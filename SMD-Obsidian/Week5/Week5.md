# Key Concepts Checklist

-  [ ] 1. GRASP Recap
	-  [ ] 1.1 Definition of GRASP
	-  [ ] 1.2 Definition of Pattern
	-  [ ] 1.3 All 9 GRASP patterns
-  [ ] 2. Indirection
	-  [ ] 2.1 Problem
	-  [ ] 2.2 Solution
	-  [ ] 2.3 Library Borrowing System example
	-  [ ] 2.4 POS / TaxMasterSystem example
	-  [ ] 2.5 Motivation and trade-offs
-  [ ] 3. Pure Fabrication
	-  [ ] 3.1 Problem
	-  [ ] 3.2 Solution
	-  [ ] 3.3 Monopoly dice-rolling example
	-  [ ] 3.4 Library BorrowingHandler example
	-  [ ] 3.5 Contraindications
	-  [ ] 3.6 Relationship to Indirection
-  [ ] 4. Polymorphism
	-  [ ] 4.1 Two problems addressed
	-  [ ] 4.2 Solution and corollary
	-  [ ] 4.3 Monopoly Square example
	-  [ ] 4.4 Discussion and contraindications
-  [ ] 5. Protected Variations
	-  [ ] 5.1 Problem
	-  [ ] 5.2 Solution
	-  [ ] 5.3 Variation point vs evolution point
	-  [ ] 5.4 POS tax calculator example
	-  [ ] 5.5 Library Item interface example
	-  [ ] 5.6 Equivalence to OCP
	-  [ ] 5.7 Contraindications
-  [ ] 6. Relationships Between GRASP Principles
	-  [ ] 6.1 Hierarchy of principles
	-  [ ] 6.2 How patterns support each other

---

# 1. GRASP Recap

## 1.1 Definition

**GRASP** — General Responsibility Assignment Software Patterns/Principles
- A set of patterns (or principles) for assigning responsibilities to objects
- Aids design reasoning in a methodical, rational, and explainable way
- Supports **Responsibility-Driven Design (RDD)**

**Pattern** — a named and well-known problem/solution pair applicable in new contexts; a recurring successful application of expertise in a particular domain

## 1.2 All 9 GRASP Patterns

| Pattern                  | This week?                    |
| ------------------------ | ----------------------------- |
| Creator                  | No (Week 4)                   |
| Information Expert       | No (Week 4)                   |
| Low Coupling             | No (Week 4)                   |
| High Cohesion            | No (Week 4)                   |
| Controller               | No (Workshop 6 supplementary) |
| **Indirection**          | ✅                             |
| **Pure Fabrication**     | ✅                             |
| **Polymorphism**         | ✅                             |
| **Protected Variations** | ✅                             |

---

# 2. Indirection

## 2.1 Problem

- Where to assign a responsibility to avoid **direct coupling** between two or more things?
- How to de-couple objects so that Low Coupling is supported and reuse potential stays higher?

##   2.2 Solution

- Assign the responsibility to an **intermediate object** to mediate between other components or services so they are not directly coupled
- The intermediary creates an _indirection_ between the other components

> [!info] Image: Two UML class diagrams side by side — left shows `Dependent Element` directly coupled to `Questionable Element` (1..*→1); right shows `Dependent Element` → `Indirection Element` → `Questionable Element`, with `+hides` association. Slide 11.

## 2.3 Library Borrowing System Example

- Design option 3 assigns `hasReachedLimit()` to `Member`
- `Member` acts as an intermediate class between `BorrowingKiosk` and `BorrowingRecord`
- `BorrowingKiosk` no longer needs a direct dependency on `BorrowingRecord`
- Result: lower coupling, higher cohesion on `BorrowingKiosk`

> [!example] Library Borrowing System — Design Option 3
> `BorrowingKiosk` → `Member` → `BorrowingRecord`
> - `Member` hides `BorrowingRecord` from `BorrowingKiosk`
> - Justified by Information Expert (last week) + Low Coupling (last week) + **Indirection** (this week)

> [!info] Image: Design model showing `BorrowingKiosk` with operations `getMember(int id)`, `listBook()`, `getBookAvailability(Book b)`, `returnBook(Book b)`, `newBorrowing(Member m, Array<Book> bs)`; `Member` with `hasReachedLimit(): boolean`, `newBorrowing(Array<Book> bs)`, `return(Book b)`; `BorrowingRecord` with `borrow(Book book)`; composition to `BorrowingItem`. Labels "Higher cohesion" and "Lower coupling" annotated. Slide 9/12.

## 2.4 POS / TaxMasterSystem Example

**Problem setup:** `Sale` needs tax from `TaxMasterSystem` (accessed via TCP)

**If `Sale` communicates directly:**
- TCP communication code embedded in `Sale`
- `Sale` highly coupled to `TaxMasterSystem`
- `Sale` less cohesive — mixes sale logic with TCP logic

**Solution by Indirection:** introduce `TaxMasterAdapter` as the intermediate class
- All TCP communication is implemented in `TaxMasterAdapter`
- `Sale` calls `taxes = getTaxes(s)` on the adapter only
- `Sale` and `TaxMasterSystem` are decoupled

> [!example] POS Tax Calculation — Indirection
> `Sale` → `:TaxMasterAdapter` ──TCP──→ `«actor» :TaxMasterSystem`
> The adapter acts as a level of indirection to external systems.

> [!info] Image: Sequence diagram showing `:Sale` calling `t = getTotal`, then `taxes = getTaxes(s)` on `:TaxMasterAdapter`, which uses TCP socket communication to reach `«actor» :TaxMasterSystem`. Slide 14.

## 2.5 Motivation and Trade-offs

- Primary motivation for Indirection is **Low Coupling** — decouple objects for future reuse
- Old adage: _"Most problems in computer science can be solved by another level of indirection"_
- Counter adage: _"Most problems in performance can be solved by removing another layer of indirection!"_

> [!note] Indirection adds a class to decouple; this can hurt runtime performance. Balance is needed.

> [!summary] Key takeaways
> - Indirection introduces an intermediate object to prevent direct coupling
> - Primary motivation is Low Coupling and increased reuse
> - `TaxMasterAdapter` and `Member` (Library) are both indirection examples
> - Adding indirection has a performance cost

---

# 3. Pure Fabrication

## 3.1 Problem

- Which object should have responsibility when you do not want to violate **High Cohesion** and **Low Coupling**, but solutions offered by other patterns (e.g., Expert) are not appropriate?
- Domain model inspires design (low representational gap), but assigning responsibilities only from the domain model often leads to poor cohesion or coupling

## 3.2 Solution

- Assign a highly cohesive set of responsibilities to an **artificial or convenience class** that does not represent a problem domain concept
- Make up a class to support high cohesion, low coupling, and reuse

> [!note] A Pure Fabrication class has no counterpart in the domain model — it is invented purely for design quality.

## 3.3 Monopoly Dice-Rolling Example

**Problem:** Who should roll the dice?
- By Expert + domain model → `MonopolyGame` should roll dice
- Acceptable, but leads to **low cohesion** in `MonopolyGame` (too many unrelated responsibilities)

**Solution:** Create artificial class `Cup`
- `Cup` is not in the Monopoly domain model
- `Cup` has `roll()` and `getTotal()`; aggregates `Die` objects (ordered)
- `Player` calls `takeTurn` → sends `roll` to `:Cup` → `:Cup` interacts with `:Board` and `loc: Square`

> [!example] Monopoly — Pure Fabrication
> `Cup` does not exist in the real Monopoly domain; invented to give dice-rolling a cohesive home.
> Class: `Cup` — `roll()`, `getTotal()` — aggregates `Die {ordered}`

> [!info] Image: Left — domain model with `Die`, `MonopolyGame`, `Board`, `Player`, `Piece`, `Square`. Right — sequence diagram showing `p:Player` calling `takeTurn`, then delegating `roll` to `:Cup` (Pure Fabrication), which calls `getTotal`, then `getSquare(loc, fvTot)` on `:Board`, then `landedOn(p)` on `loc:Square`. Slide 24.

## 3.4 Library BorrowingHandler Example

**Problem:** If `BorrowingKiosk` becomes the UI controller, who handles application logic (`newBorrowing()`, `returnItem()`, `getItemAvailability()`)?

- With High Cohesion and Low Coupling in mind, none of the existing domain classes are suitable

**Solution:** Create `BorrowingHandler` — a Pure Fabrication
- `BorrowingKiosk` focuses on user input only: `memberLogin()`, `listItem()`, `returnitem()`, `newBorrowing()`
- `BorrowingHandler` owns: `assignCurMember(int id)`, `listItem()`, `getAvailability(Item b)`, `returnItem()`, `newBorrowing(Array<Items> bs)`

> [!info] Image: Design model with `BorrowingKiosk` → `BorrowingHandler` → `Member`, `BorrowingRecord`, `BorrowingItem`. `BorrowingHandler` is a new class not present in the domain model. Slide 26.

## 3.5 Contraindications

- Pure Fabrication must **not** be used as an excuse to add new objects arbitrarily
- If overused: design ends up with one class per responsibility → too many behaviour objects
- Responsibilities become separated from the information needed to fulfill them → increases coupling

## 3.6 Relationship to Indirection

| Concept | Meaning |
|---|---|
| **Indirection** | Assign _any_ class (domain or artificial) as an intermediary |
| **Pure Fabrication** | Assign an _artificial_ class (not in the domain model) as an intermediary |

Many Pure Fabrications arise because of Indirection — e.g., `TaxMasterAdapter` is both a Pure Fabrication (not in the domain) and an Indirection (mediates between `Sale` and `TaxMasterSystem`).

> [!summary] Key takeaways
> - Pure Fabrication invents a class not in the domain model to preserve cohesion and low coupling
> - `Cup` (Monopoly) and `BorrowingHandler` (Library) are canonical examples
> - Overuse leads to scattered responsibilities and increased coupling
> - Most Pure Fabrications also serve as Indirection

---

# 4. Polymorphism

## 4.1 Two Problems Addressed

**Problem 1 — Handling type-based alternatives:**
- Behaviour varies based on type/condition
- Without polymorphism: if-then-else or switch statements scattered across the codebase → low cohesion, bloated classes

**Problem 2 — Pluggable software components:**
- Client-Server: how to replace the Server without affecting the Client?

## 4.2 Solution

- When related alternatives or behaviours vary by type, assign responsibility for the behaviour — using **polymorphic operations** — to the types for which the behaviour varies
- **Polymorphic operation** — an operation with the same interface across multiple types
- **Corollary:** Do not test for the type of an object and use conditional logic to perform varying alternatives based on type

## 4.3 Monopoly Square Example

**Use case:** When a player lands on a square, action depends on square type:

| Square type | Action |
|---|---|
| `GoSquare` | Player receives $200 (`addCash(200)`) |
| `RegularSquare` | Do nothing |
| `IncomeTaxSquare` | `w = getNetWorth()`, `reduceCash(min(200, 10% of w))` |
| `GoToJailSquare` | (further logic) |

**Analysis:** Alternatives based on type → use Polymorphism

**Approach:**
- **Step 1: Identify types that vary** — `GoSquare`, `RegularSquare`, `IncomeTaxSquare`, etc.
- **Step 2: Identify the varying operation** — `landedOn(p)` (triggered when a player lands on a square)
- **Step 3: Make it polymorphic** — declare `landedOn {abstract}` on abstract `Square`; each subclass implements its own version

> [!info] Image: Updated domain model showing `Square` with subclasses `GoSquare`, `RegularSquare`, `IncomeTaxSquare`, `GoToJailSquare`. Slide 32.

> [!info] Image: Partial design model — abstract `Square{abstract}` with `landedOn {abstract}`; subclasses `RegularSquare`, `GoSquare`, `IncomeTaxSquare` each implementing `landedOn`. `Player` has association `location` to `Square`. Slide 34.

> [!info] Image: Sequence diagram — `p:Player` calls `takeTurn`; loops `roll` on `dice:Die`, `fv = getFV`; calls `loc = getSquare(loc, fvTot)` on `:Board` (by Expert); calls `landedOn(p)` on `loc:Square` — dispatches polymorphically. Sub-diagram shows three `alt` branches for `GoSquare`, `RegularSquare`, `IncomeTaxSquare` instances. Slide 35.

> [!example] Monopoly Polymorphism
> `loc.landedOn(p)` dispatches to the correct `Square` subclass at runtime — no switch/if-else needed in `Player` or `Board`.

> [!note] Using switch/if-else inside `Square` instead of polymorphism would produce low cohesion and a bloated class — Corollary explicitly forbids this.

## 4.4 Discussion and Contraindications

- Polymorphism is a _fundamental_ pattern for handling similar variations — design is easily extended to handle new types
- **Contraindication:** Speculative "future-proofing" with polymorphism at points where variation is improbable may waste effort; unnecessary complexity can outweigh the benefit

> [!summary] Key takeaways
> - Use polymorphic operations when behaviour varies by type — avoids if/switch proliferation
> - Enables pluggable components (swappable servers without client changes)
> - Monopoly's `landedOn()` is the core example
> - Avoid speculative polymorphism where variation is unlikely

---

# 5. Protected Variations

## 5.1 Problem

- How to design objects, subsystems, and systems so that **variations or instability** in these elements does not have an undesirable impact on other elements?

## 5.2 Points of Change

| Term                | Meaning                                                       |
| ------------------- | ------------------------------------------------------------- |
| **Variation point** | Variations in the existing system or requirements (known now) |
| **Evolution point** | Speculative variations that may arise in the future           |

## 5.3 Solution

**Step 1:** Identify points of known or predicted variation or instability
**Step 2:** Assign responsibilities to create a **stable interface** around them

> [!note] "Interface" here means _any_ means of access — not only a Java/programming language `interface` keyword.

## 5.4 POS Tax Calculator Example

**Scenario:** POS currently uses `TaxMasterSystem`; in the future, other tax calculators might be used.

**Analysis:**
1. **Variation/instability point:** Different interfaces or APIs of external tax calculators (TCP vs API, etc.)
2. **Stable interface:** Use Indirection (`TaxMasterAdapter`) to expose a stable `getTaxes(Sale)` to `Sale`
3. **Behaviour of `getTaxes()` varies by calculator type** → apply Polymorphism: make `getTaxes()` a polymorphic operation

**Result:** Define `«interface» ITaxCalculatorAdapter` with `getTaxes(Sale): List<TaxLineItems>`

Implementations:
- `TaxMasterAdapter` — TCP communication
- `GoodAsGoldTaxProAdapter` — software package via API
- `<???>Adapter` — future calculators

> [!info] Image: Class diagram — `«interface» ITaxCalculatorAdapter` with `getTaxes(Sale): List<TaxLineItems>`; implemented by `TaxMasterAdapter`, `GoodAsGoldTaxProAdapter`, `<???>Adapter`. Annotations: "Indirection level protects the variation of interfaces", "Polymorphism protects the variation of behaviors". Slide 40.

![[Pasted image 20260612210053.png]]
> [!example] POS Protected Variation
> `Sale` only ever calls `getTaxes(s)` on an `ITaxCalculatorAdapter` — it is shielded from all concrete calculator changes.
> Adding a new tax system = add a new adapter class, zero change to `Sale`.

## 5.5 Library Item Interface Example

**Scenario:** A member might borrow items other than `Book` (e.g., `Video`); each item type has different attributes.

- **Variation point:** `Book` is known to vary (more item types coming)
- **Solution:** Create `«Interface» Item` with stable operations: `getTitle()`, `getDetail()`, `getQuantity()`
- `Book` and `Video` both implement `Item`
- `getDetail()` is a polymorphic operation — each subtype implements its own version

All other classes (`BorrowingKiosk`, `BorrowingRecord`, `BorrowingItem`, `Member`) now reference `Item`, not `Book` directly.

> [!info] Image: Design model — `«Interface» Item` with `getTitle(): String`, `getDetail(): String`, `getQuantity(): int`; implemented by `Book` and `Video`. `BorrowingKiosk`, `Member`, `BorrowingRecord`, `BorrowingItem` all reference `Item`. Annotation: "Protected variation" on `BorrowingItem`, "Polymorphism" on `Book`/`Video`. Slide 43.

![[Pasted image 20260613133922.png]]

## 5.6 Equivalence to OCP

**Protected Variation** is equivalent to the **Open-Closed Principle (OCP)** (Bertrand Meyer):
- **OCP:** Modules should be both _open_ (for extension; adaptable) and _closed_ (to modification that affects clients)
- A class can be closed w.r.t. attribute access (via access methods only), but open to modification of underlying attributes and addition of new methods

## 5.7 Discussion and Contraindications

- PV is a **root principle** motivating most mechanisms and patterns in programming and design (e.g., Core PV Mechanisms, Data-Driven Design, Service Lookup, Interpreter-Driven Design)
- PV, Low Coupling, and High Cohesion are the **fundamental principles** we aim to achieve; the other 6 GRASP patterns are tools to achieve them
- **Contraindication:** Same as Polymorphism — speculative future-proofing at evolution points can cost more than it saves; reworking a simpler brittle design may be cheaper

> [!summary] Key takeaways
> - Identify variation/evolution points; wrap them in a stable interface
> - Combine Indirection + Polymorphism to implement PV robustly
> - Equivalent to OCP — open for extension, closed to client-impacting modification
> - Overuse at speculative evolution points wastes effort

---

# 6. Relationships Between GRASP Principles

## 6.1 Hierarchy

- **Goal principles** (what we aim for): **Protected Variation**, **Low Coupling**, **High Cohesion**
- **Tool patterns** (how we achieve them): Creator, Information Expert, Controller, Polymorphism, Indirection, Pure Fabrication

## 6.2 How Patterns Support Each Other

- **Low Coupling** → achieves protection at a variation point → supports **Protected Variation**
- **Polymorphism** → achieves Low Coupling + protection at a variation point → supports both **Low Coupling** and **Protected Variation**
- **Indirection** → is supported by **Controller** (Controller is a specialised Indirection)
- **Expert** → is supported by **Creator**
- **Pure Fabrication** → supports **Low Coupling** and **High Cohesion**

> [!info] Image: Dependency graph — `Protected Variation` at top; `Low Coupling` and `High Cohesion` in middle tier; `Polymorphism`, `Indirection`, `Pure Fabrication`, `Expert` in lower tier; `Controller` feeds into `Indirection`; `Creator` feeds into `Expert`. Arrows represent "supports/achieves". Slide 45.

> [!summary] Key takeaways
> - Protected Variation, Low Coupling, High Cohesion are the top-level goals
> - All other GRASP patterns are means to those goals
> - Patterns commonly combine: e.g., Indirection + Polymorphism implement Protected Variation; a Pure Fabrication often is also an Indirection