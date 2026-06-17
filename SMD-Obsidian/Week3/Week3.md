# Key Concepts Checklist

-  [ ] **1. OO Software Design**
	-  [ ] 1.1 Definition and purpose
	-  [ ] 1.2 Role in the UP artifact pipeline
	-  [ ] 1.3 Layers in OO design

-  [ ] **2. Static Design Models — Design Class Diagrams (DCD)**
	-  [ ] 2.1 Definition
	-  [ ] 2.2 DCD notation elements
	-  [ ] 2.3 Domain model vs design model
	-  [ ] 2.4 Mapping DCD to code

-  [ ] **3. Responsibility-Driven Design (RDD)**
	-  [ ] 3.1 Definition and motivation
	-  [ ] 3.2 Knowing responsibilities
	-  [ ] 3.3 Doing responsibilities
	-  [ ] 3.4 Collaboration
	-  [ ] 3.5 Relationship to domain model

-  [ ] **4. Dynamic Design Models — Design Sequence Diagrams (DSD)**
	-  [ ] 4.1 Definition
	-  [ ] 4.2 DSD notation elements
	-  [ ] 4.3 System Sequence Diagram vs Design Sequence Diagram
	-  [ ] 4.4 RDD and DSD
	-  [ ] 4.5 Mapping DSD to code

- [ ] **5. Lucky 7 Case Study**
	-  [ ] 5.1 Design Class Diagram
	-  [ ] 5.2 Design Sequence Diagram

---

# 1. OO Software Design

## 1.1 Definition and Purpose

- **Object-oriented software design**: process of creating a conceptual solution by defining *software objects* and their collaboration
- Concerns:
  - Structure and connectedness between software objects and architectural levels
  - Interfaces: methods, data types, protocols (external and internal)
  - Assignment of responsibilities — via principles and patterns
- Two model types produced:
  - *Static*: **Design Class Diagram (DCD)**
  - *Dynamic*: **Design Sequence Diagram (DSD)**

> [!info] Image: 
> Slide 7 — overview diagram showing how use case text, SSD, operation contracts, and domain model all feed into the Design Model (DCD + DSD)

<center><img src="Pasted image 20260405152155.png" alt="" width="500" height="350"></center>

## 1.2 Role in the UP Artifact Pipeline

- **Use case text** → describes functional requirements the design must realise
- **Domain models** → inspire names and relationships of software objects
- **System Sequence Diagrams** → identify starting events to design for and user inputs/system responses
- **Operation contracts** → supply detailed postconditions that the design must satisfy
- **Glossary / Supplementary Specification** → item details, formats, validation, non-functional requirements

> [!info] Image: 
> Slide 6 — "Sample UP Artifact Relationships" showing the full chain from Business Modelling → Requirements → Design

<center><img src="Pasted image 20260405170957.png" alt="" width="500" height="700"></center>


## 1.3 Layers in OO Design

| Layer | Examples | Focus |
|---|---|---|
| User Interface | Store GUI | Minor — connects to app logic |
| **Application logic** | `Sale`, `Payment` | **Primary focus** |
| Other layers/components | Logging, Database Access | Secondary — covered in future lectures |

- OO design of core logic layer is similar across technologies
- Essential OO design skills transfer to other layers
- Other layers tend to be technology/platform dependent and changeable

> [!summary] Key takeaways
> - OO design = define software objects + their collaboration
> - Two model types: static (DCD) and dynamic (DSD)
> - UP artifacts (use cases, domain model, SSD) all feed into the design
> - Primary focus is the application logic layer

---

# 2. Static Design Models — Design Class Diagrams (DCD)

## 2.1 Definition

- **Static design model**: 
	- representation of software objects defining class names, attributes, and method signatures (*not* method bodies)
- Visualised using a **UML Class Diagram**

## 2.2 DCD Notation Elements

| Element                        | Description                                                      |
| ------------------------------ | ---------------------------------------------------------------- |
| Class name                     | Name of the software class                                       |
| Attributes and data type       | e.g. `time : DateTime`, `isComplete : Boolean`, `total : Double` |
| Interfaces (method signatures) | e.g. `endSale()`, `enterItem(...)`, `makePayment(...)`           |
| Association with multiplicity  | e.g. `1` to `currentSale`                                        |

> [!note] Method *bodies* are **not** included in DCDs — only signatures

> [!info] Image: 
> Slide 10 — DCD showing `Register` linked via `currentSale` (multiplicity 1) to `Sale`, with attributes and method signatures labelled

![[Pasted image 20260405172311.png]]

## 2.3 Domain Model vs Design Model

| | Domain Model | Design Model (DCD) |
|---|---|---|
| Perspective | Conceptual — problem domain | Implementation — software |
| Focus | Noteworthy concepts, attributes, associations | Roles and collaborations of software objects |
| UML notation | Class diagram | Class diagram |
| Methods shown? | No | Yes (signatures) |

- Domain models **inspire** class names, attributes, and associations in the design model
- Goal: **reduce the representational gap** between how stakeholders conceive the domain and its software representation

> [!example] POS system:
> - Domain `Sale` has `time` attribute → software `Sale` class has `time : DateTime`
> - Domain `SaleLineItem` has `quantity` → software `SalesLineItem` has `quantity : Integer`

> [!info] Image: 
> Slide 11 — side-by-side Domain Model and DCD for `Register`/`Sale`, with arrow labelled "Inspire terms and associations"

![[Pasted image 20260405171628.png]]

![[Pasted image 20260518122244.png]]

> [!summary] Key takeaways
> - DCD = class names + attributes + method signatures (no bodies)
> - Domain model inspires but does not equal the design model
> - Representational gap is reduced when domain terms are reused in software

## 2.4 Mapping DCD to Code

- Attributes in DCD → Java **fields**
- Method signatures in DCD → Java **methods**
- **Create responsibility** → Java **constructor**

> [!example] `SalesLineItem` mapping:
> - DCD: `quantity : Integer`, `getSubtotal() : Money`, association to `ProductDescription`
> - Java: `private int quantity;`, `private ProductDescription description;`, constructor `SalesLineItem(ProductDescription desc, int qty)`, method `public Money getSubtotal()`

> [!info] Image: 
> Slide 19 — DCD for `SalesLineItem` with dotted lines connecting each element to the corresponding Java code

![[Pasted image 20260405172437.png]]

> [!note] A collection class (e.g. `ArrayList`) is needed when a class must maintain visibility to multiple instances — e.g. `Sale` holding many `SalesLineItem` objects via `private List lineItems = new ArrayList();`

---

# 3. Responsibility-Driven Design (RDD)

## 3.1 Definition and Motivation

- **Responsibility-Driven Design (RDD)**: approach to designing software objects by assigning *responsibilities* to them
- **Responsibility**: the obligations or behaviours of an object in terms of its role
- Metaphor: software objects are like people with responsibilities who collaborate with others to get work done

## 3.2 Knowing Responsibilities

- Knowing about private encapsulated data
- Knowing about related objects
- Knowing about things it can derive or calculate

## 3.3 Doing Responsibilities

- Doing something itself (e.g. creating an object, doing a calculation)
- Initiating action in other objects
- Controlling and coordinating activities in other objects

## 3.4 Collaboration

- Responsibilities are implemented by methods that either act alone or collaborate with other methods and objects

> [!note] A responsibility is an *abstraction* — it is not the same thing as a method. Methods *fulfil* responsibilities.

## 3.5 Relationship to Domain Model

- Domain model often inspires **knowing** responsibilities → achieves low representational gap
- Example: domain `Sale` has `/total` → software `Sale` knows its total → implements `getTotal()`
- Example: domain `SaleLineItem` has `quantity` → software `SalesLineItem` knows `quantity`

> [!example] POS — assigning responsibilities:
> - `Sale` is responsible for *knowing* its total → `getTotal()` method
> - `Sale` is responsible for *creating* `SalesLineItem` objects → `makeLineItem()` method
> - `getTotal()` on `Sale` *collaborates* with `getSubtotal()` on `SalesLineItem`

> [!info] Image: Slide 13 — partial domain model (top) and partial DCD (bottom) for `Sale`/`SalesLineItem`, showing how domain attributes map to design attributes and methods

<center><img src="Pasted image 20260405235617.png" alt="" width="500" height="500"></center>

> [!summary] Key takeaways
> - RDD: assign knowing and doing responsibilities to objects
> - Methods fulfil responsibilities; responsibilities are the abstraction
> - Domain model inspires knowing responsibilities → low representational gap
> - Collaboration = methods working together across objects

---

# 4. Dynamic Design Models — Design Sequence Diagrams (DSD)

## 4.1 Definition

- **Dynamic design model**: representation of how software objects interact via *messages*
- Visualised using **UML Sequence Diagrams** (Communication diagrams also exist but are not covered)
- **Design Sequence Diagram**: illustrates the sequence/time ordering of messages sent between software objects

## 4.2 DSD Notation Elements

| Element | Description |
|---|---|
| Class name | Box at top of lifeline, e.g. `: Register`, `: Sale` |
| Message with parameter | Solid arrow with label, e.g. `makePayment(cashTendered)` |
| Found message | Filled circle origin — sender not specified |
| Activation/execution bar | Narrow rectangle on lifeline — shows focus of control (optional) |
| Object creation | Underlined message name, e.g. `create(cashTendered)` → new object box |
| Return value | Dashed arrow, labelled with return, e.g. `fv1 = getFaceValue() :int` |

> [!info] Image: Slide 21 — DSD showing `:Register` sending `makePayment(cashTendered)` to `:Sale`, which then sends `create(cashTendered)` to a new `:Payment` object; labels identify each notation element

![[Pasted image 20260405172535.png]]
## 4.3 System Sequence Diagram vs Design Sequence Diagram

| | System Sequence Diagram (SSD) | Design Sequence Diagram (DSD) |
|---|---|---|
| View of system | Black box | Internal — objects exposed |
| Participants | Actor + `:System` | Software objects (e.g. `:Register`, `:Sale`) |
| Focus | Actor–system interaction | Object–object message interaction |
| Messages | System operations (e.g. `enterItem(itemID, quantity)`) | Internal method calls |

> [!info] Image: Slide 22 — side-by-side SSD (left, cashier interacting with `:System`) and partial DSD (right, `:Register` → `:Sale` → `:Payment`), with arrow showing `makePayment` zoomed into the DSD

![[Pasted image 20260406001715.png]]
## 4.4 RDD and DSD

- Drawing a DSD forces explicit decisions about responsibility assignment
- When an object sends a `create(...)` message, it implies that object has the *responsibility* to create the new instance

> [!example] In the DSD, `:Sale` sends `create(cashTendered)` to `:Payment` → implies `Sale` has the doing responsibility to create `Payment` objects

> [!info] Image: Slide 23 — DSD showing `Sale` creating `Payment`, with annotation "Implies that Sale object have a responsibility to create Payments"

![[Pasted image 20260406001751.png]]
## 4.5 Mapping DSD to Code

- Each message sent from object A to object B → A calls a method on B
- Object held as a field enables repeated message sends
- **Step 1** Object A has a field referencing B: `private B myB = new B();`
- **Step 2** Method in A calls B's methods in sequence order from the diagram

> [!example] DSD: `:A` sends `doOne` (found), then sends `doTwo` and `doThree` to `myB:B`
> ```java
> public class A {
>     private B myB = new B();
>     public void doOne() {
>         myB.doTwo();
>         myB.doThree();
>     }
> }
> ```

> [!info] Image: Slide 28 — DSD with `:A` and `myB:B` alongside the equivalent Java class A code

> [!summary] Key takeaways
> - DSD shows message passing between software objects (not actors)
> - SSD = black box view; DSD = internal view
> - Drawing a DSD makes responsibility assignments explicit
> - DSD maps directly to fields and method calls in code

---

# 5. Lucky 7 Case Study

**Use case**: Player rolls 2 dice. Win if total face value = 7; otherwise lose.

## 5.1 Design Class Diagram

| Class | Attributes | Methods |
|---|---|---|
| `DiceGame` | `- die1 :Die`, `- die2 :Die` | `+ play() :boolean` |
| `Die` | `- faceValue :int` | `+ getFaceValue() :int`, `+ roll() :void` |

- Association: `DiceGame` → `Die`, labelled `includes`, multiplicity `1` to `2`

> [!info] Image: Slide 18 — completed DCD with `DiceGame` and `Die` boxes, `includes` association, all attributes and methods filled in

> [!note] `die1` and `die2` as separate named attributes reflect the 1-to-2 association from the domain model. Both are typed `:Die`.

## 5.2 Design Sequence Diagram

Participants: `:DiceGame`, `die1 :Die`, `die2 :Die`

- **Step 1** Found message `play() :boolean` arrives at `:DiceGame`
- **Step 2** `:DiceGame` sends `roll()` to `die1 :Die`
- **Step 3** `:DiceGame` sends `fv1 = getFaceValue() :int` to `die1 :Die`
- **Step 4** `:DiceGame` sends `roll()` to `die2 :Die`
- **Step 5** `:DiceGame` sends `fv2 = getFaceValue() :int` to `die2 :Die`
- **Step 6** Return found message `:fv1 + fv2 == 7` (dashed arrow back)

> [!info] Image: Slide 27 — completed DSD for Lucky 7 showing all 6 steps above with activation bars and labelled return

> [!example] Responsibility mapping for Lucky 7:
> - `DiceGame` has *doing* responsibility: roll both dice and calculate outcome → `play()` method
> - `Die` has *doing* responsibility: randomise its own face value → `roll()` method
> - `Die` has *knowing* responsibility: know its current face value → `getFaceValue()` method

> [!summary] Key takeaways
> - Lucky 7 DCD: two classes, `DiceGame` owns two `Die` instances, `play()` returns boolean
> - Lucky 7 DSD: `DiceGame` orchestrates rolling and reading each die in sequence
> - Responsibilities map cleanly: `Die` knows/rolls itself; `DiceGame` coordinates and decides outcome