# Key Concepts Checklist

-  [ ] **1. Software Architecture**
	-  [ ] 1.1 Definition
	-  [ ] 1.2 Decisions involved
	-  [ ] 1.3 Why it matters

-  [ ] **2. Architectural Analysis**
	-  [ ] 2.1 Definition and goal
	-  [ ] 2.2 What it analyses
	-  [ ] 2.3 Architecturally significant functional requirements
	-  [ ] 2.4 Architecturally significant non-functional requirements
	-  [ ] 2.5 Effects of requirements on design
	-  [ ] 2.6 Common steps
	-  [ ] 2.7 Priorities

-  [ ] **3. Architectural Factor Table**
	-  [ ] 3.1 Definition and fields
	-  [ ] 3.2 POS example

-  [ ] **4. Technical Memo**
	-  [ ] 4.1 Definition and fields
	-  [ ] 4.2 POS example

-  [ ] **5. Logical Architecture**
	-  [ ] 5.1 Definition
	-  [ ] 5.2 Relationship to UP artifacts
	-  [ ] 5.3 Layers definition
	-  [ ] 5.4 Strict vs relaxed layered architecture
	-  [ ] 5.5 Layers and partitions (UML Package Diagram)
	-  [ ] 5.6 Common IS layers
	-  [ ] 5.7 Guidelines
	-  [ ] 5.8 SSDs and layers
	-  [ ] 5.9 Benefits of layered architecture
	-  [ ] 5.10 Domain layer / domain model relationship
	-  [ ] 5.11 Model-View Separation Principle
	-  [ ] 5.12 Package nesting alternatives
	-  [ ] 5.13 Mapping packages to code

-  [ ] **6. Implementation View Architecture**
	-  [ ] 6.1 Component definition
	-  [ ] 6.2 Component Diagram
	-  [ ] 6.3 UML component notation
	-  [ ] 6.4 Interfaces: required and realized

-  [ ] **7. Separation of Concerns**
	-  [ ] 7.1 Cross-cutting concerns
	-  [ ] 7.2 Techniques

---

# 1. Software Architecture

## 1.1 Definition

- **Software Architecture**: the large-scale organisation of elements in a software system
- A design that meets high-impact system requirements, supports business logic, and is straightforward to maintain and evolve

## 1.2 Decisions Involved

| Decision type       | Question                                          |
| ------------------- | ------------------------------------------------- |
| Structural elements | What should the software classes/components be?   |
| Interfaces          | How are elements composed together?               |
| Collaboration       | How do elements work together per business logic? |
| Composition         | How are elements grouped into larger subsystems?  |

## 1.3 Why It Matters

- Designing on partial requirements → architecture collapse later
- Ignoring business logic → misfit between components (puzzle pieces that don't fit)

> [!summary] Key takeaways
> - Architecture = large-scale organisation of a software system
> - Involves structural, interface, collaboration, and composition decisions
> - Bad architecture causes coupling cascades and business logic misalignment

---

# 2. Architectural Analysis

## 2.1 Definition and Goal

- **Architectural Analysis**: an activity to identify factors that will influence the architecture, understand their variability and priority, and resolve them
- Identifies and resolves **non-functional requirements** in the context of **functional requirements**
- Goal: reduce risk of missing a critical factor, focus effort on high-priority requirements, align product with business goals

## 2.2 What It Analyses

- **Architecturally significant requirement**: a requirement with large impact on the design (especially when not considered early)
- **Variation points**: e.g. varying discount strategies in the POS
- **Potential evolution points**: anticipated future changes

## 2.3 Architecturally Significant Functional Requirements

| Function          | Description                                               |
| ----------------- | --------------------------------------------------------- |
| Auditing          | Provide audit trails of system execution                  |
| Licensing         | Track, acquire, install, and monitor license usage        |
| Localization      | Support multiple human languages                          |
| Mail              | Send and receive mail                                     |
| Online help       | Provide online help capability                            |
| Printing          | Provide printing facilities                               |
| Reporting         | Provide reporting facilities                              |
| Security          | Protect access to resources or information                |
| System management | Manage applications in a distributed environment          |
| Workflow          | Support moving documents including review/approval cycles |

## 2.4 Architecturally Significant Non-Functional Requirements

- **Usability** — aesthetics and consistency in the UI
- **Reliability** — availability (system uptime), calculation accuracy, recovery from failure
- **Performance** — throughput, response time, recovery time, start-up/shutdown time
- **Supportability** — testability, adaptability, maintainability, compatibility, configurability, installability, scalability, localizability

## 2.5 Effects of Requirements on Design

> [!example] POS system:
> - *Reliability/fault-tolerance*: if remote tax calculator fails, should it failover to a local service?
> - *Adaptability/configurability*: selling POS to many retailers with varying business rules — should POS be configurable?
> - *Branding*: retailers want their own brand in the POS UI — should branding be changeable?
>
> Answers significantly change the design.

## 2.6 Common Steps

Occurs in early elaboration:

**Step 1** Identify/analyse **architectural factors** — requirements with impact on architecture (especially NFRs)
- Overlaps with requirements analysis
- Some identified during inception, now investigated in more detail

**Step 2** For each architectural factor, analyse alternatives and create **architectural decisions**
- e.g. remove requirement; custom solution; stop project; hire expert

## 2.7 Priorities

1. **Inflexible constraints** — safety, security, legal compliance
2. **Business goals** — demo deadline, competitor window-of-opportunity
3. **Other goals** — all requirements eventually trace back to business goals (e.g. extendible → new release every 6 months)

> [!summary] Key takeaways
> - Architectural Analysis = identify and resolve architecturally significant requirements
> - Two steps: identify factors, then decide solutions
> - Priority order: inflexible constraints → business goals → other goals

---

# 3. Architectural Factor Table

## 3.1 Definition and Fields

- Documentation that records the influence of factors, their priorities, and their variability (immediate flexibility and future evolution)

| Field | Description |
|---|---|
| Factor | Name of the architectural factor |
| Measures and quality scenarios | Concrete, measurable scenario for the factor |
| Variability | Current flexibility and anticipated future evolution |
| Impact | Impact on stakeholders, architecture, and other factors |
| Priority for success | High / Medium / Low |
| Difficulty or risk | High / Medium / Low |

## 3.2 POS Example — Reliability: Recoverability

> [!example] Reliability — Recoverability of POS
> - **Factor**: Recovery from remote service (e.g., Tax Calculator) failure
> - **Measures/quality scenario**: When remote service fails, re-establish connectivity within 1 min. of detected re-availability, under normal store load in production
> - **Variability (current)**: Local client-side simplified services are acceptable (and desirable) until reconnection is possible
> - **Variability (evolution)**: Within 2 years, some retailers may pay for full local replication of remote services. Probability: High.
> - **Impact**: High impact on large-scale design — retailers dislike when remote services fail as it prevents sales
> - **Priority**: High
> - **Difficulty/Risk**: Medium

> [!info] Image: Slide 16 — Architectural Factor Table for POS Reliability–Recoverability with all 6 fields filled in

![[Pasted image 20260617165033.png]]

---

# 4. Technical Memo

## 4.1 Definition and Fields

- Documentation that records alternative solutions, decisions, influential factors, and motivations for noteworthy architectural decisions

| Field | Content |
|---|---|
| Issue | The architectural problem being addressed |
| Solution Summary | Brief statement of the chosen solution |
| Factors | Relevant architectural factors driving the decision |
| Solution | Detailed description of the chosen approach |
| Motivation | Why this solution was chosen |
| Unresolved Issues | Outstanding open questions |
| Alternatives Considered | Other options that were evaluated and rejected |

## 4.2 POS Example — Remote Service Failure Recovery

> [!example] Technical Memo: Reliability — Recovery from Remote Service Failure
> - **Issue**: Reliability — Recovery from Remote Service Failure
> - **Solution Summary**: Location transparency using service lookup, failover from remote to local, partial local replication
> - **Factors**: (a) robust recovery from remote service failure, e.g. tax calculator; (b) robust recovery from remote product DB failure, e.g. prices and descriptions
> - **Solution**:
>   - Use smart `Proxy` objects; on each call, test for remote service reactivation and redirect when possible
>   - Offer local implementations of remote services (e.g. small cache for tax rates)
>   - Achieve protected variation w.r.t. service location using an `Adapter` created in a `ServicesFactory`
> - **Motivation**: Retailers don't want to stop making sales. This design also supports future replication evolution. Full third-party tax calculator not replicated due to high licensing costs and weekly configuration overhead.
> - **Unresolved Issues**: None
> - **Alternatives Considered**: "Gold level" QoS agreement with remote credit authorisation — available but too expensive

> [!summary] Key takeaways
> - Factor table: captures what a factor is and how important/risky it is
> - Technical memo: captures how it was resolved and why
> - Both are UP elaboration-phase artefacts

---

# 5. Logical Architecture

## 5.1 Definition

- **Logical Architecture (LA)**: the large-scale organisation of software classes into packages, subsystems, and layers
- Not concerned with networking, physical computers, or OS processes (those belong to the **deployment architecture**)
- LA defines the packages in which software classes are defined

## 5.2 Relationship to UP Artifacts

- Influenced by constraints and NFRs in the **Supplementary Specification**
- Visualised using **UML Package Diagrams** within the Design Model
- Packages organised into layers alongside class diagrams (static view) and interaction diagrams (dynamic view)

> [!info] Image: Slide 22/40 — UP artifact relationships diagram showing how Supplementary Specification influences the Design Model (UI → Domain → Tech Services layered package diagram)

## 5.3 Layers Definition

- **Layer**: coarse-grained grouping of classes, packages, or subsystems with cohesive responsibility for a major aspect of the system

## 5.4 Strict vs Relaxed Layered Architecture

| Type | Description | Typical use |
|---|---|---|
| **Strict** | A layer only calls the layer directly below it | Network protocol stacks |
| **Relaxed** | A higher layer may call upon several lower layers | Information systems (common) |

## 5.5 Layers and Partitions (UML Package Diagram)

- **Layers** = vertical grouping (top = high app-specific, bottom = low general services)
- **Partitions** = horizontal grouping within a layer (e.g. `Sales`, `Payments`, `Taxes` within `Domain`)
- Dependency arrows (dashed) go from higher layers to lower layers

> [!info] Image: Slide 24 — UML Package Diagram showing UI (Swing, Web), Domain (Sales, Payments, Taxes), Technical Services (Persistence, Logging, RulesEngine), with vertical layer arrows and horizontal partition labels

## 5.6 Common IS Layers

| Layer | AKA | Contents / Responsibilities |
|---|---|---|
| **UI** | Presentation, View | GUI windows, reports, speech interface, HTML/XML/JSP |
| **Application** | Workflow, Process, App Controller | Handles presentation requests, workflow, session state, page transitions |
| **Domain** | Business, Application Logic, Model | Domain rules, domain services (POS, Inventory) |
| **Business Infrastructure** | Low-level Business Services | Very general low-level business services, e.g. `CurrencyConverter` |
| **Technical Services** | Technical Infrastructure | High-level frameworks — Persistence, Security |
| **Foundation** | Core Services, Low-level Infrastructure | Low-level services — data structures, threads, math, file/DB/network I/O |

> [!note] Width of each layer box in the diagram implies its range of applicability. Foundation is the widest (most general across systems).

> [!info] Image: Slide 25 — Full IS Logical Architecture layer stack with annotations and vertical dependency arrow

## 5.7 Guidelines

- Organise into distinct cohesive layers from high (app-specific) to low (general services)
- **Maintain separation of concerns** — no application logic in UI objects
- Coupling flows from higher to lower layers — **lower to higher coupling is avoided**
- **Don't show external resources (e.g. databases) as the bottom layer** — use a logical package representation instead

> [!example] Worse vs Better (POS):
> - Worse: `MySQL Inventory` shown as a layer — mixes logical and deployment views
> - Better: `Inventory` shown as a package in the Domain layer (logical representation abstracting the DB implementation)

> [!info] Image: Slide 27 — side-by-side "Worse" (MySQL in layers) and "Better" (logical package with `«component»` Novell LDAP) package diagrams

## 5.8 SSDs and Layers

- System operations in an SSD represent operation calls on the **Application or Domain layer** from the **UI layer**
- The UI action triggers method calls on a **Façade Controller** (e.g. `Register`) in the Domain layer

> [!info] Image: Slide 28 — SSD (cashier → :System) mapped to layered architecture: UI Swing `ProcessSaleFrame` → Domain `Register` (Façade Controller), annotated with `makeNewSale()`, `enterItem()`, `endSale()`

## 5.9 Benefits of Layered Architecture

Addresses these problems:
- Changes rippling through system due to high coupling
- Intertwining of application logic and UI (reduces reuse, restricts distribution)
- Intertwining of general technical services with app-specific logic (reduces reuse, complicates replacement)
- High coupling across concerns, impairing division of development work

## 5.10 Domain Layer / Domain Model Relationship

- UP Domain Model = stakeholder's view of noteworthy concepts → **inspires** Domain layer class names and structure
- `Payment` in the Domain Model is a concept; `Payment` in the Design Model is a software class — former inspired latter

> [!note] They are **not** the same thing. Inspiration reduces the representational gap — one of the core ideas in OO design.

> [!info] Image: Slide 46 — Domain Model (`Payment`, `Sale`, `Pays-for`) above the Design Model (`Payment` with `amount: Money`, `getBalance(): Money`; `Sale` with `date: Date`, `getTotal(): Money`) with "inspires objects and names in" arrow

## 5.11 Model-View Separation Principle

1. **Don't couple non-UI objects directly to UI objects** — non-UI objects should be reusable and attachable to a new UI
2. **Don't put application logic in UI object methods** — UI objects should only: initialise UI elements, receive UI events, delegate application logic requests to non-UI objects

## 5.12 Package Nesting Alternatives

| Notation | How it looks |
|---|---|
| **Embedded Packages** | Packages drawn inside parent package box |
| **UML Fully-Qualified Names** | e.g. `UI::Swing`, `Domain::Sales` |
| **Circle-Cross Symbol (⊕)** | Parent shown with ⊕ connecting to children |

> [!info] Image: Slide 42 — three equivalent UML package nesting notations side by side

## 5.13 Mapping Packages to Code

> [!example] NextGen POS Java package mapping:
> ```java
> // UI Layer
> com.mycompany.nextgen.ui.swing
> com.mycompany.nextgen.ui.web
> // Domain Layer
> com.mycompany.nextgen.domain.sales
> com.mycompany.nextgen.domain.payments
> // Technical Services Layer
> com.mycompany.service.persistence
> org.apache.log4j
> // Foundation Layer
> com.mycompany.util
> ```

> [!summary] Key takeaways
> - LA = large-scale organisation into packages, subsystems, layers — not physical deployment
> - Visualised with UML Package Diagrams
> - Coupling flows top-down only; no application logic in UI
> - Domain model inspires Domain layer class names, reducing representational gap

---

# 6. Implementation View Architecture

## 6.1 Component Definition

- **Component**: a modular part of a system that encapsulates its contents and is replaceable within its environment
- Can be a class, an external resource (DB), or a third-party service

## 6.2 Component Diagram

- Concerns how to implement the software system at a high level
- Provides the initial architecture landscape
- Defines behaviour in terms of **provided** and **required** interfaces
- Extends logical architecture by including external resources (e.g. databases, messaging services)

## 6.3 UML Component Notation

| Element | Meaning |
|---|---|
| Rectangle with `«component»` | A component |
| **Lollipop** (filled circle on line) | Provided interface — component realises/implements this |
| **Socket** (open arc) | Required interface — component depends on/uses this |
| `«system»` | A system-level component |

> [!example] NextGen POS:
> - `«system» NextGen` requires `SQL` → connects to `«component» DB`
> - `«system» NextGen` requires `JMS` → connects to `MessagingService`

> [!info] Image: Slide 31 — UML component diagram for NextGen POS with lollipop/socket notation for SQL (DB) and JMS (MessagingService)

## 6.4 Interfaces: Required and Realized

| Notation | Meaning |
|---|---|
| Lollipop (circle end) | **Provided interface** — component realises this interface |
| Socket (open arc end) | **Required interface** — component uses/requires this interface |
| Dashed dependency arrow | Component has a dependency on the interface |

> [!example] `Timer` interface example:
> - `Clock1` → lollipop → implements `Timer` (provided interface)
> - `Window1` → socket → uses `Timer` (required interface)
> - `Window2` → dashed arrow → depends on `Timer` when collaborating with `Clock2`

> [!info] Image: Slide 35 — diagram showing lollipop, socket, and dependency notations for `«interface» Timer` with Clock1/2/3 and Window1/2/3

> [!summary] Key takeaways
> - Component diagram = high-level implementation view including external resources and services
> - Lollipop = provided interface; socket = required interface
> - Complements the logical architecture package diagram with physical/deployment-adjacent detail

---

# 7. Separation of Concerns

## 7.1 Cross-Cutting Concerns

- **Cross-cutting concerns**: concerns with wide application or influence across the entire system
- Examples: data persistence, security, logging

## 7.2 Techniques

1. **Modularize** into a separate component/subsystem
2. **Use decorators** — a container that wraps the inner object
3. **Use post-compilers or aspect-oriented technology**

> [!note] Separation of concerns applies at both the architectural level (layered separation) and small scale (class design). The same principle underpins GRASP's Low Coupling and the no-app-logic-in-UI guideline.
