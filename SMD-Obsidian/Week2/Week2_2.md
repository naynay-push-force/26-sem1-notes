# Key Concepts Checklist

- [ ] **1. Object-Oriented Analysis**
    - [ ] 1.1 Purpose of OO analysis
    - [ ] 1.2 Static vs dynamic OO models
    - [ ] 1.3 Relationship between use cases, domain models, SSDs, and design

- [ ] **2. Domain Models**
    - [ ] 2.1 Definition of a domain model
    - [ ] 2.2 Domain model as a visual dictionary
    - [ ] 2.3 Domain model versus software/design model
    - [ ] 2.4 UML class diagrams as a notation for domain models

- [ ] **3. Conceptual Classes**
    - [ ] 3.1 Definition of a conceptual class
    - [ ] 3.2 Symbol, intension, and extension
    - [ ] 3.3 Strategies for identifying conceptual classes
    - [ ] 3.4 Category-list method
    - [ ] 3.5 Noun-phrase method and its dangers

- [ ] **4. Associations**
    - [ ] 4.1 Definition of an association
    - [ ] 4.2 When an association should be included
    - [ ] 4.3 Association names and multiplicities
    - [ ] 4.4 Reading-direction arrows

- [ ] **5. Attributes**
    - [ ] 5.1 Definition of an attribute
    - [ ] 5.2 When to include attributes
    - [ ] 5.3 Derived attributes

- [ ] **6. Similar Concepts and Class Hierarchies**
    - [ ] 6.1 Generalization and specialization
    - [ ] 6.2 Superclasses and subclasses
    - [ ] 6.3 Subclasses vs attributes
    - [ ] 6.4 Guidelines for creating subclasses

- [ ] **7. System Sequence Diagrams (SSDs)**
    - [ ] 7.1 Definition of an SSD
    - [ ] 7.2 One SSD per scenario
    - [ ] 7.3 System events and external actors
    - [ ] 7.4 Black-box view of the system
    - [ ] 7.5 Return values and loop frames
    - [ ] 7.6 Inter-system events

- [ ] **8. Relationship Between SSDs and Domain Models**
    - [ ] 8.1 Domain model as static context
    - [ ] 8.2 SSD as dynamic context
    - [ ] 8.3 How use case text drives both

---

# 1. Object-Oriented Analysis

## 1.1 What this week is about

Week 2 shifts from use cases → OO analysis. 
Analysis = investigating the problem and its requirements. 
OO analysis = describing the domain from the perspective of objects.

- **Use cases** → what users are trying to achieve
- **Domain models** → important concepts and relationships in the problem world
- **SSDs** → the sequence of system events in a particular use case scenario

## 1.2 Static and dynamic OO models

|Model|Type|Captures|
|:--|:--|:--|
|Domain Class Diagram|Static|Concepts, attributes, relationships|
|System Sequence Diagram|Dynamic|Actor actions, system events, ordering of interactions|

## 1.3 How this fits into the wider software process

**Use Case Text → Domain Model → SSDs → Design Model**

Analysis artifacts sit between requirements and design. They translate user goals into a structured understanding of the problem before implementation.

> [!summary] Key takeaways
> 
> - OO analysis is about the **problem domain**, not the software solution
> - Domain models and SSDs complement each other: one static, one dynamic
> - Use case text is the primary input to both

---

# 2. Domain Models

## 2.1 Definition

**Definition:**

- A representation of real-world conceptual classes
- Shows noteworthy domain concepts/objects
- **Not** software objects — not necessarily the classes you'll code
- Also known as *UP Business Object Model* — focuses on things and products important to the business domain

## 2.2 Domain model as a visual dictionary

- Answers: what things matter in this domain, what are they called, what information do we care about, how are they related
- Think: _"What things exist in this problem space?"_ — not _"What classes should I code?"_

## 2.3 Domain model vs design model

|Domain model|Design model|
|:--|:--|
|`Sale`, `Register`, `Payment`, `ProductDescription`|`RegisterController`, `ProductCatalogService`, `SaleRepository`|
|Real-world concepts|Software implementation structures|
|Problem understanding|Solution construction|

## 2.4 UML class diagrams — conceptual use

- Domain models use UML class diagram notation, but **conceptually**
- Shows: conceptual classes, attributes, associations
- **No operations (method signatures)** — domain model UML class diagrams do not include methods

> [!info] Image: Partial POS Domain Model 
> - rectangles for `Register`, `Store`, `Sale`, `Payment`, `SalesLineItem`, `Item`; 
> - attributes in each; association lines with multiplicities

<center><img src="Pasted image 20260316135443.png" alt="" width="500" height="350"></center>

![[Pasted image 20260318163213.png]]


> [!summary] Key takeaways
> 
> - Domain model = **conceptual model**, not code structure
> - UML notation for **concepts, attributes, associations only**
> - **No methods**

---

# 3. Conceptual Classes

## 3.1 Definition

**Definition:**

- An idea, thing, or object — any important domain concept worth representing
- May have both a data role and a behavioural role in the domain

Examples: `Sale`, `Payment`, `Register`, `Item`, `Customer`, `Cashier`

## 3.2 Symbol, intension, and extension

A conceptual class can be broken down into:

- **Symbol**: the words/images representing the concept
- **Intension**: the definition of the concept
- **Extension**: the set of examples that belong to the concept

For `Sale`:

- **Symbol**: "Sale"
- **Intension**: a purchase transaction event with date and time
- **Extension**: `sale-1`, `sale-2`, `sale-3`, ...

<center><img src="Pasted image 20260316142007.png" alt="" width="400"></center>

## 3.3 How to identify conceptual classes

Three strategies:

#### 3.3.1 Use existing models

If the domain resembles something standard (inventory, finance, booking, retail), existing model patterns may help.

#### 3.3.2 Use a category list

Ask whether the domain has concepts from these categories:

- business transactions
- physical objects
- containers of things
- places where transactions are recorded

<center><img src="Pasted image 20260316145711.png" alt="" width="400"></center>

#### 3.3.3 Identify noun phrases

- Scan use case text for noun phrases — but **not mechanically**
- Words in natural language are ambiguous; not every noun becomes a class
- Some nouns become attributes, values, or descriptive terms instead

## 3.4 Step-by-step: identifying conceptual classes from use case text

Using the POS main success scenario:

**Step 1: Read for important domain nouns** 
- From "Customer arrives, Cashier starts a new sale, enters item identifier, System records sale line item...":
- → `customer`, `cashier`, `sale`, `item`, `sale line item`, `item description`

**Step 2: Group by category**

- Role of people/org → `Customer`, `Cashier`
- Product/services → `Item`
- Business transaction → `Sale`
- Transaction line item → `SalesLineItem`
- Description of things → `ProductDescription`
- Where transaction is recorded → `Register`

**Step 3: Rename vague phrases** "Goods and/or services" → `Item`

**Step 4: Keep only noteworthy concepts** Not every noun makes the cut — focus on what's needed to understand the domain.

> [!summary] Key takeaways
> 
> - Use case text is a starting point, not a mechanical rule
> - Category lists help spot missing concepts
> - Ask: _"Is this concept important enough to preserve in the model?"_

---

# 4. Associations

**Definition:**

- A relationship between classes that indicates a **meaningful and interesting connection**
- Not every imaginable link — only ones significant to the domain

## 4.1 When to include an association

Include if the association is:

- Significant in the domain
- A relationship whose knowledge must be preserved
- Suggested by common association lists

Guiding question: "does the system need to remember this relationship independently?"
## 4.2 Association names and multiplicities

Example: `Register` → `Sale` with association name `Records-current`

Multiplicity values:

- `1` = exactly one
- `0..1` = optional, at most one
- `1..*` = one or more
- `*` = many

![[Pasted image 20260316150523.png]]

**Reading direction arrow** — has no meaning except to indicate direction for reading the association label. Often excluded. Not the same as navigability in design diagrams.

## 4.3 Deciding whether to add an association

1. Does the relationship matter to domain understanding? (e.g. does `Sale` relate to `Payment`? Yes)
2. Does the system need to remember it? (which payment paid which sale?)
3. Add multiplicity (e.g. a sale may be paid by one payment; a register records many sales)
4. Name it if that improves clarity — use domain language

> [!summary] Key takeaways
> 
> - Only meaningful associations
> - Multiplicity is essential — adds precise domain meaning
> - Reading arrows are label-reading aids only


![[Pasted image 20260518120842.png]]

---

# 5. Attributes

**Definition:**

- A **logical data value of an object**
- Include when requirements/use cases suggest a need to remember information

POS examples:

- `Register`: `id`
- `Store`: `name`, `address`
- `Sale`: `dateTime`, `/total`
- `CashPayment`: `amountTendered`
- `ProductDescription`: `itemID`, `description`, `price`
- `Cashier`: `id`

## 5.1 Derived attributes

- Marked with a `/` prefix (e.g. `/total`)
- Value is **computed** from other stored information, not independently stored
- `Sale /total` is derived from its line items and prices

> [!info] Image: Derived Attribute Notation  `
> - Sale` class box with `Date`, `Time`, `/total : 
> - `Money` in attributes compartment

<center><img src="Pasted image 20260318144251.png" alt="" width="400"></center>


## 5.2 Identifying attributes from use case text

**Step 1: Find information the system must remember**

- "item identifier" → `itemID` on `ProductDescription`
- "price" → `price` on `ProductDescription`
- "date/time of sale" → `dateTime` on `Sale`

**Step 2: Put the attribute in the right class**

- `price` belongs on `ProductDescription`, not on `Register`

**Step 3: Basic or derived?**

- "running total" → likely derived (`/total`)

> [!summary] Key takeaways
> 
> - Attributes are logical data values tied to a class
> - Put attributes in the most appropriate class
> - Use `/` prefix for derived attributes

---

# 6. Similar Concepts and Class Hierarchies

## 6.1 Generalization and specialization

When domain concepts share common structure but also differ, organise them into a hierarchy:

- **Superclass** = the general concept
- **Subclasses** = specialized concepts
- Subclasses relate to superclasses as set membership (a `CashPayment` is a `Payment`)

Example:

- `Payment` (superclass)
    - `CashPayment`
    - `CreditPayment`
    - `CheckPayment`

Benefits: economy of expression, reduction in repeated information, improved comprehension.

## 6.2 Subclasses vs attributes

The key modeling judgment:

|Use **subclasses** when...|Use an **attribute** when...|
|:--|:--|
|Variants have different attributes|Variants only differ by label/category|
|Variants have different associations|No structural/behavioural difference|
|Variants behave differently in noteworthy ways||

Library example:

- `Book` and `Video` → subclasses of `LibraryItem` — they differ in attributes (`authors` vs `duration`)
- `Student`, `Staff`, `Guest` → **not** subclasses of `User` — no behavioural or structural difference; use a `kind` attribute instead

## 6.3 Guidelines for creating subclasses

Create a subclass if the subclass has:

1. Additional attributes of interest
2. Additional associations of interest
3. Behaviour that is handled differently in noteworthy ways

Modeling conventions:

- Declare superclasses **abstract**
- Append the superclass name to the subclass name (e.g. `CashPayment`, not just `Cash`)

> [!info] Image: Payment hierarchy + Library Item hierarchy 
> - `Payment` → `CashPayment`, `CreditPayment`, `CheckPayment`; 
> - `LibraryItem` → `Book`, `Video`

<center><img src="Pasted image 20260318144956.png" alt="" width="400"></center>

![[Pasted image 20260318145132.png]]

> [!summary] Key takeaways
> 
> - Not every category deserves a subclass
> - Subclasses when differences matter in structure or behaviour
> - Attributes for simple categorical variation

---

# 7. System Sequence Diagrams (SSDs)

## 7.1 Definition

**Definition:**

- A visualisation of the system events that external actors generate, the order of those events, and possible inter-system events
- One SSD per **scenario** (not per use case)
- Treats the system as a **black box**

## 7.2 Black-box view

- SSDs show **what** events occur, not **how** the system handles them internally
- You show `makeNewSale`, `enterItem(itemID, quantity)` — you do **not** show internal objects collaborating
    - That comes later in design sequence diagrams

## 7.3 Elements of an SSD

- Actor lifeline
- System lifeline (single black-box)
- Incoming messages from actor → system (with parameters)
- Optional dashed return messages (system → actor)
- Loop frames with boolean guards for repeated steps

## 7.4 Inter-system events

SSDs can also show interactions between systems — e.g. the POS system calling:

- `CreditAuthorizationService`
- `Accounts`
- `TaxCalculator`

External software systems can be actors too.

> [!info] Image: POS Process Sale SSD 
> - `:Cashier` lifeline on left, `:System` lifeline on right; 
> - `makeNewSale()`, loop frame `[more items]` with `enterItem(itemID, qty)` → `description, total`, 
> - then `endSale()` → `total with taxes`, 
> - then `makePayment(amount)` → `change due, receipt`


> [!info] Image: Myki Quick Top-up SSD 
> - `:Traveler` and `:Top-up Machine`; 
> - `enterCard(number)` → `currentBalance`, 
> - `enterTopUp(amount)`, 
> - `makePayment(creditCard)`, 
> - `enterCard(number)` → `currentBalance`

> [!summary] Key takeaways
> 
> - One SSD = one scenario
> - System is always a black box
> - Only actor↔system interactions — no internal design
> - External software systems can be actors

---

# 8. Deriving an SSD from Use Case Text

Using the POS cash-only Process Sale scenario:

**Step 1: Read the scenario line by line** Find explicit interactions between actor and system.

**Step 2: Extract external input events** From the cashier:

- `makeNewSale`
- `enterItem(itemID, quantity)`
- `endSale`
- `makePayment(amount)`

**Step 3: Add system responses where the scenario says something is displayed**

- `description, total`
- `total with taxes`
- `change due, receipt`

**Step 4: Add control structures** Items are entered repeatedly → wrap in `loop [more items]`

**Step 5: Keep the system black-box** Do not show `Sale`, `ProductCatalog`, or any internal class on the SSD.

---

# 9. Relationship Between Domain Models and SSDs

|              | Domain model                       | SSD                                         |
| :----------- | :--------------------------------- | :------------------------------------------ |
| Captures     | Concepts, attributes, associations | System events, order, parameters, responses |
| Type         | Static                             | Dynamic                                     |
| Derived from | Use case nouns                     | Use case actor-system interactions          |

Both are derived from the same use case text — domain concepts come from the nouns, SSD events come from the interactions.

**SSD parameters reflect domain model concepts:**

- `enterItem(itemID, quantity)` — `itemID` and `quantity` are attributes of domain classes
- `makePayment(amount)` — `amount` is an attribute of `Payment`

> [!summary] Key takeaways
> 
> - Build them together, not in isolation
> - Use case text drives both
> - SSD message parameters often come directly from domain concepts and attributes
> - Together they prepare the way for design

---

# 10. Summary

- OO analysis describes the **problem domain** from the perspective of objects
- **Domain model** = conceptual classes, attributes, associations — no methods
- **SSD** = sequence of system events for one scenario — black-box system
- Both expressed at an **abstract level** to help understand the domain in the context of current requirements

### Quick reference

|Concept|Key point|
|:--|:--|
|Domain model|Conceptual — not software objects, no methods|
|Conceptual class|Idea, thing, or object worth representing|
|Association|Meaningful relationship worth preserving|
|Attribute|Logical data value worth remembering|
|Derived attribute|Computed value, marked with `/` e.g. `/total`|
|Subclass|Use when variants differ in attributes, associations, or behaviour|
|SSD|Black-box, one per scenario, actor-generated events only|
|Static|Domain model|
|Dynamic|SSD|