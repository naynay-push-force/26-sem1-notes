# Key Concepts Checklist

- [ ] **1. Use Cases: Purpose and Big Picture**
	- [ ] 1.1 Use cases as a requirements discovery/recording tool
	- [ ] 1.2 "Text stories" focused on user goals and perspective
	- [ ] 1.3 Learning objectives: forms, diagrams, include/extend, tests

- [ ] **2. Core Definitions and Vocabulary**
	- [ ] 2.1 System-under-Discussion (SuD)
	- [ ] 2.2 Actor (as "something with behaviour")
	- [ ] 2.3 Scenario (use case instance)
	- [ ] 2.4 Use Case (collection of success/failure scenarios for a goal)

- [ ] **3. Actor Types and Why They Matter**
	- [ ] 3.1 Primary actor (goals drive the use case)
	- [ ] 3.2 Supporting actor (provides services; clarifies external interfaces)
	- [ ] 3.3 Offstage actor (has interests; ensures interests satisfied)

- [ ] **4. Use Case Text and Levels of Detail**
	- [ ] 4.1 Example use case text: "Process Sale" (POS)
	- [ ] 4.2 Capturing success + failure scenarios
	- [ ] 4.3 "Casual" vs more detailed styles (Handle Returns example)

- [ ] **5. Use-Case Model and Influence on Development**
	- [ ] 5.1 Use cases influence design/implementation/project management
	- [ ] 5.2 Use-case model: written use cases (+ optionally UML diagram)
	- [ ] 5.3 Relationship to other artifacts (domain model, SSDs, contracts, etc.)

- [ ] **6. UML Use Case Diagrams**
	- [ ] 6.1 Purpose: context diagram of system + environment
	- [ ] 6.2 Core notation: actors, use cases, system boundary, communication links
	- [ ] 6.3 Example diagrams: POS + Monopoly Simulation System

- [ ] **7. Finding Useful Use Cases**
	- [ ] 7.1 Boss Test
	- [ ] 7.2 Elementary Business Process (EBP) Test
	- [ ] 7.3 Size Test
	- [ ] 7.4 Applying tests: Negotiate Supplier Contract, Handle Returns, Log In, Move Piece

- [ ] **8. Summary Principles**
	- [ ] 8.1 Use cases = actor achieves goal (text)
	- [ ] 8.2 Diagrams give context + relationships
	- [ ] 8.3 Cover success + failure; use different detail levels; apply tests

---

# 1. Use Cases: Purpose and Big Picture

## 1.1 Use cases as a requirements discovery/recording tool

**Analysis:** 
Use cases are widely used to discover and record requirements.

**Intuition / role in design:**
- It is a structured way to capture *what the system must do for users* in order to achieve their goals. 
- Instead of starting with classes, APIs, or screens, you start with *user value* and *observable system behaviour*.
	- The behaviour is all about how your systems responds to inputs and events

## 1.2 Use cases as "text stories" driven by goals

**Definition:** 
- Use cases are *text stories of some actor using a system to meet goals*, emphasizing "user goals and perspective."

**Interpretation:**
* **"Text stories"** means narrative sequences: actor actions + system responses, step by step.
* **"Goals"** means you're capturing meaningful outcomes (not UI clicks or micro-actions).

## 1.3 Learning objectives for this topic

From the slides, by the end you should be able to:
* interpret use cases in different forms and use case diagrams
* relate use cases using **include/extend** (not fully elaborated in this lecture deck)
* apply tests to identify suitable use cases

> [!summary] Key takeaways / best practices
> *   Write use cases to capture **functional requirements** from the **user's** viewpoint.
> *   Prefer **goal-level** use cases over tiny "button press" tasks.
> *   Use diagrams optionally for **context**, not as a substitute for the written story.

---

# 2. Core Definitions and Vocabulary

## 2.1 System-under-Discussion (SuD)

**Definition:** 
- SuD = "System-under-Discussion." 
	- The system boundary you are analysing.
	- It forces clarity about what is **inside** vs **outside** the system.

## 2.2 Actor

**Definition:**
- Something with behaviour, such as a person, computer system or organisation
- Actors are **external** to the SuD, but they interact with it. 
- Treat an actor as a role with goals, not necessarily a single individual.

## 2.3 Scenario (use case instance)

**Definition:** 
- A specific sequence of actions and interactions between actors and the SuD
- A scenario is one concrete "run" through the story (one path). A single use case usually contains multiple scenarios.

## 2.4 Use Case

**Definition:** 
- A use case is a collection of related success and failure scenarios that describe an actor using the SuD to support a goal.
- A use case is a **goal container**: it gathers the main success path plus alternative/error paths.

> [!summary] Key takeaways / best practices
> *   **Scenario** = one path; **Use case** = set of related paths.
> *   Always name use cases with a **goal** (verb phrase + business meaning).
> *   Actors are *outside* the system boundary; they are not internal components.

---

# 3. Actor Types and Why They Matter

## 3.1 Primary actor

**Definition:** 
- Has their user goals fulfilled through using services of the SuD. 
	- User goals drive the use case

**How to use this in practice:**
1.  Identify who initiates the interaction to achieve a goal.
2.  Name the use case around **that goal** (not around a UI operation).

## 3.2 Supporting actor

**Definition:** 
- A supporting actor provides a service (e.g. information) to the SuD to clarify external interfaces and protocols.
	- Often a computer system.

**How to use this in practice:**
* List external systems/services the SuD depends on (e.g., payment authorization).
* Document interactions that must occur at system boundaries.

## 3.3 Offstage actor

**Definition:** 
- Has interest in the behaviour of the use case but is not primary/supporting.
- It helps "ensure all interests identified/satisfied."

**How to use this in practice:**
* Ask: "Who cares about the outcome, audit trail, compliance, reporting?"
* Use offstage actors to avoid missing non-functional or regulatory needs.

> [!summary] Key takeaways / best practices
> *   **Primary actor** = goal owner.
> *   **Supporting actor** = service provider.
> *   **Offstage actor** = stakeholder interest, often driving constraints and required records.

---

# 4. Use Case Text and Levels of Detail

## 4.1 Example: "Process Sale" (POS)

The lecture provides a "Process sale" narrative for a POS checkout.

<center><img src="Pasted image 20260306235102.png" alt="" width="400"></center>
### 4.1.1 Use case structure

Below is a clean "textbook" structure, filled using the lecture story.

> [!example] Use Case: Process Sale (POS)
> **Primary Actor:** Cashier and Customer (drives the interaction to complete the sale).
> **Supporting Actors:** Tax calculator; Accounting system; Inventory system.
> **Goal:** Complete a customer purchase and record all sale/payment details. 
> **Scope (SuD):** POS system.
> 
> **Main Success Scenario (MSS):**
> 1. Customer arrives at checkout with items to purchase. 
> 2. Cashier starts a new sale.
> 3. Cashier enters an item identifier. 
> 4. System records the sale line item and presents item description, price, and running total (price based on price rules). 
> 5. Repeat item entry until done. 
> 6. System presents total with taxes calculated by an external tax calculator system. 
> 7. Cashier asks for payment; customer pays; system handles payment. 
> 8. System logs completed sale and sends sale/payment information to external accounting and inventory systems; system presents receipt. 
> 9. Customer leaves with receipt and goods. 
> 
> **Alternative Scenarios / Extensions (pattern):**
> * The lecture emphasizes that use cases include success and failure scenarios. 
> * Example extension style appears in "Handle Returns"
> 
> *When you write extensions, specify:*
> *   "Trigger condition (e.g., 'payment rejected')"
> *   "System + actor responses"
> *   "Outcome (recover, cancel, fallback path)"

## 4.2 Example: Handle Returns (casual) + alternative scenarios

The lecture provides "Handle Returns" as a **casual** use case and shows that alternative scenarios are captured alongside the main success scenario.

A main success scenario for Handle Returns use case for POS:
- A customer arrives at a checkhout with items to return.
- The cashier uses the POS system to record each returned item ...

**What "casual" implies (from the example):**
* Short, narrative description ("A customer arrives... the cashier uses the POS system...")
* Followed by alternative paths like:
	* If the customer paid by credit, and the reimbursement transaction their credit account is rejected, inform the customer and pay them with cash.
	* If the system detects failure to communicate with the external accounting system, ...

## 4.3 Success + failure scenarios are mandatory

The summary explicitly states: "Use Cases should cover both success and failure scenarios." 

> [!summary] Key takeaways / best practices
> *   Use cases are **not only "happy paths."** Capture realistic failures.
> *   Keep the "Main Success Scenario" readable; push exceptions into extensions.
> *   Choose detail level based on purpose (early discovery vs precise specification).

---

# 5. Use-Case Model and Influence on Development

## 5.1 Use cases influence design, implementation, project management

- Use cases influence many aspects of a software development project
	- Design,
	- Implementation, and 
	- Project Management
- Use cases are key inputs to OO analysis and testing
- Use cases should be strongly driven by the goals of the project

**Intuition:**
- Use cases become the "spine" that connects requirements to design artifacts and test scenarios.

## 5.2 Use-Case Model

**Definition:** 
- A Use-Case Model is "a model of the system's functionality and environment"
	- *Primarily* includes the set of all written use cases
	- *Optionally* includes a UML use case diagram.

<center><img src="Pasted image 20260307113430.png" alt="" width="500"></center>

## 5.3 Artifact relationships (how use case text connects forward)

The diagram in the lecture shows use cases feeding into system sequence diagrams, operation contracts, domain models, and design models. 

>[!info] Visual: "Sample UP Artifact Relationships" diagram (page with POS artifacts)
> The center highlights **Use Case Text** (e.g., "Process Sale: 1. Customer arrives... 2. Cashier makes new sale..."). Arrows link the **Use-Case Model** to downstream artifacts: 
> *   **System Sequence Diagrams** showing actor-to-system events (e.g., `makeNewSale()`, `enterItem(id, quantity)`),
> *   **Operation Contracts** describing operation post-conditions, 
> *   **Domain Model** with conceptual classes (e.g., Sale, SalesLineItem), 
> *   **Design Model** showing interactions among objects (e.g., Register calls ProductCatalog to getProductSpec, then adds line item to Sale).

<center><img src="Pasted image 20260307113126.png" alt="" width="500"></center>

> [!summary] Key takeaways / best practices
> *   Use cases are a **requirements anchor**: they inform analysis models and test cases.
> *   The use-case model is mostly **text**; the diagram is optional.
> *   Traceability: each major use case should map to design/test artifacts.

---
# 6. UML Use Case Diagrams

## 6.1 Purpose: context diagram of the system and its environment

**Definition:** 
- A UML use case diagram gives a context diagram of a system and its environment.
	- Showing names of use cases, actors, and relationships.

<center><img src="Pasted image 20260307113824.png" alt="" width="500"></center>

## 6.2 Core notation (as shown in the POS diagram)

From the slide content: actors, use cases, system boundary, and communication links are explicitly labelled.
*   **Actor:** stick figure (or `«actor»` stereotype for external systems)
*   **Use case:** ellipse with a name (e.g., Process Sale, Handle Returns)
*   **System boundary:** rectangle enclosing use cases (e.g., "NextGen POS")
*   **Communication association:** line between actor and use case

## 6.3 POS example diagram (descriptive placeholder)

> [!info] Visual: UML Use Case Diagram for a "NextGen POS" system
> - A large rectangle labeled **NextGen POS** encloses multiple ellipses (use cases) such as 
> 	- **Process Sale**, 
> 	- **Handle Returns**, 
> 	- **Cash In**, 
> 	- **Analyze Activity**, 
> 	- **Manage Security**, 
> 	- **Manage Users**, and an ellipsis "..." indicating additional use cases. 
> - On the left and right of the system boundary are actors: human roles like 
> 	- **Customer**, 
> 	- **Cashier**, 
> 	- **Manager**, 
> 	- **System Administrator**, 
> - plus external-system actors shown with `«actor»` stereotypes such as 
> 	- **Payment Authorization Service**,
> 	- **Tax Calculator**, 
> 	- **Accounting System**, 
> 	- **HR System**, 
> 	- and **Sales Activity System**. 
> - Straight lines connect each actor to the use cases in which they participate (communication associations).

## 6.4 Monopoly simulation example (descriptive placeholder)

> [!info] Visual: Simplified UML Use Case Diagram for a "Monopoly Simulation System"
> - A system boundary contains one main use case like **Play Monopoly Game**. 
> - An external **observer** actor is drawn outside and connected to that use case by a single communication line,
> 	- indicating interaction with the system. 

> [!summary] Key takeaways / best practices
> *   Use-case diagrams are **context maps**, not behavior specs.
> *   Use them to communicate scope (actors + top-level goals).
> *   Keep them readable: avoid diagram overload; let text carry detail.

---

# 7. Finding Useful Use Cases

*"What is a useful level to express use cases for application requirements analysis?"*

## 7.1 Boss Test

**Idea:** 
- If your boss asks what you did all day and you answer "Logging in!", would they be happy?
- Is "Log in" use case useful?

**Step-by-step: how to apply**
1. Take a candidate use case name (e.g., "Log In").
2. Imagine reporting it as a day's work to a stakeholder.
3. If it sounds like a *mere step* rather than valuable work, it's likely too low-level.
4. Reframe into a goal-level use case (e.g., "Access Account Features Securely") only if it represents meaningful value in your domain, otherwise treat login as a step/extension.

**Example (from slides)**
* "Log In" fails the boss test ("Boss will not be happy if this is all you do all day!").

## 7.2 Elementary Business Process (EBP) Test

**Definition:** 
- An EBP is a task performed by one person in one place at one time, in response to a business event, which adds measurable business value and leaves the data in a consistent state.

**Step-by-step: how to apply**
1. **One person?** Identify the primary actor—can one person complete it?
2. **One place + one time?** Is it a cohesive unit of work, not spread across contexts/timeframes?
3. **Triggered by a business event?** (e.g., customer returns an item; customer wants to buy items)
4. **Adds measurable value?** (refund issued; sale completed)
5. **Leaves data consistent?** System records are coherent at the end (sale recorded; inventory updated).

**Examples (from slides)**
* "Handle Returns" seems like an EBP.
* "Delete a line item" is questioned as potentially not reflecting an EBP (often a step, not a full business process).

## 7.3 Size Test

**Idea:** 
- A task is seldom a single action/step; use cases typically involve many steps; fully dressed can be 3–10 pages. 

**Step-by-step: how to apply**
1. Write the "Main Success Scenario" as numbered steps.
2. If it's basically one step ("Move piece"), it's too small.
3. If it balloons into a very long negotiation spanning lots of sub-goals, it may be too large and should be decomposed or treated at a different level (business vs system use case).

**Examples (from slides)**
* "Move Piece on Game Board" fails: single step.
* "Negotiate a Supplier Contract" is broader/longer than an EBP and may be a **business** use case rather than a system use case. 

> [!summary] Key takeaways / best practices
> *   **Boss test** guards against **too small** / trivial use cases.
> *   **EBP test** targets **business-value**, cohesive interactions.
> *   **Size test** ensures the use case is neither a micro-action nor an unbounded mega-process.

---

# 8. Summary Principles

From the lecture summary:
*   Use cases are text descriptions of an actor using the system to achieve a goal. `[01-2-UseCases]`
*   Use case diagrams provide context and show relationships between use cases and actors.
*   Use cases should cover both success and failure scenarios.
*   Use cases can be at different levels of detail; use tests to identify useful ones. 

