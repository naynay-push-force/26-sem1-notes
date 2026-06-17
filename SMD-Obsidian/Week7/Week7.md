# Key Concepts Checklist

-  [ ] **1. GoF Design Patterns — Overview**
	-  [ ] 1.1 History and origin
	-  [ ] 1.2 Three categories
	-  [ ] 1.3 Patterns covered in this course

-  [ ] **2. Façade (GoF)**
	-  [ ] 2.1 Problem and solution
	-  [ ] 2.2 Generalised structure
	-  [ ] 2.3 POS rule engine example
	-  [ ] 2.4 ATM example

-  [ ] **3. Decorator (GoF)**
	-  [ ] 3.1 Problem and solution
	-  [ ] 3.2 General form and recursive composition
	-  [ ] 3.3 Collaboration and sequencing
	-  [ ] 3.4 Shape Decorator example

-  [ ] **4. Builder (GoF)**
	-  [ ] 4.1 Problem and solution
	-  [ ] 4.2 Generic structure and sequence
	-  [ ] 4.3 Juice example

---

# 1. GoF Design Patterns — Overview

## 1.1 History and Origin

- **Design patterns** in software: observing and documenting recurring solutions, not inventing new ones
- Inspired by Christopher Alexander's architectural pattern language (1977)

| Year | Event |
|---|---|
| 1977 | Christopher Alexander publishes *A Pattern Language* |
| 1987 | Cunningham & Beck apply Alexander's ideas to Smalltalk |
| 1990 | Gang of Four (Gamma, Helm, Johnson, Vlissides) begin cataloguing patterns |
| 1995 | GoF publish *Design Patterns: Elements of Reusable Object-Oriented Software* |

- **Gang of Four (GoF)**: named for its four authors; patterns based around C++

## 1.2 Three Categories

| Category | Count | Concern |
|---|---|---|
| Creational | 5 | Abstracting the object instantiation process |
| Structural | 7 | How classes/objects combine to form larger structures; relationships between them |
| Behavioural | 11 | Communication and interaction between objects |

## 1.3 Patterns Covered in This Course

- Façade, Decorator, Builder (this lecture)
- Adapter, Singleton, (Concrete) Factory, Composite (upcoming)
- Template, Strategy, Observer (revisited from SWEN20003)
- Not all 23 GoF patterns are covered

> [!note] GRASP principles are a _generalisation_ of GoF design patterns — understanding GRASP helps recognise the intent behind GoF patterns

> [!summary] Key takeaways
> - Patterns capture expert solutions to recurring problems — no need to reinvent
> - Three GoF categories: Creational, Structural, Behavioural
> - This course covers a subset of 23 GoF patterns

---

# 2. Façade (GoF)

## 2.1 Problem and Solution

**Problem:** Require a unified interface (set access points/methods) to a disparate/varied set of implementations or interfaces (e.g., within a subsystem). Direct coupling to many subsystem objects is undesirable; implementation may change.

**Solution:** Define a single point of contact — a **façade** object that wraps the subsystem. Presents one unified interface and handles all collaboration with subsystem components internally.

- Category: _Structural_
- Façades are frequently set up as a **Singleton** (single globally-accessible instance)

## 2.2 Generalised Structure

- Without Façade: `ClientClass` objects couple directly to multiple subsystem `Class` objects — high coupling
- With Façade: all `ClientClass` objects talk only to `Facade`; `Facade` delegates internally

> [!info] Image: Slide 12 — side-by-side class diagrams: "Without Facade" (many arrows from ClientClass to subsystem classes) vs "With Facade" (all arrows route through a single Facade class into the subsystem)

![[Pasted image 20260613181323.png]]

> [!note] The Façade may expose only a _subset_ of subsystem functionality — it is tailored to what external clients actually need

## 2.3 POS Rule Engine Example

**Context:** NextGen POS customers want pluggable business rules that invalidate certain operations (e.g., gift card restricts to one item; charitable sale restricts items >$250 unless cashier is manager).

**Analysis:**
- Customisation should have _low impact_ on existing components
- Need a "rule engine" subsystem — implementation TBD (could be Strategy, open-source, or commercial)
- Façade hides the implementation choice from the rest of the system

**Design:**
- Package `POSRuleEngine` contains `+ POSRuleEngineFacade` (public) and `- IRule`, `- Rule1`, `- Rule2` (private to package)
- `POSRuleEngineFacade` is a Singleton: `instance : RuleEngineFacade`, `getInstance() : RuleEngineFacade`
- Key methods: `isInvalid(SalesLineItem, Sale)`, `isInvalid(Payment, Sale)`

> [!info] Image: Slide 13 — UML Package Diagram showing `Domain` package (`Sale`, `Register`) and `POSRuleEngine` package; `POSRuleEngineFacade` (public) exposes `isInvalid` and holds a `*` association to `«interface» IRule`, which `Rule1` and `Rule2` implement

![[Pasted image 20260613213750.png]]

> [!example] Using the Façade in `Sale.makeLineItem`:
> ```java
> public void makeLineItem(ProductDescription desc, int quantity) {
>     SalesLineItem sli = new SalesLineItem(desc, quantity);
>     // call to the Facade
>     if (POSRuleEngineFacade.getInstance().isInvalid(sli, this))
>         return;
>     lineItems.add(sli);
> }
> ```
> `Sale` never knows which rule engine is in use — only the Façade does

## 2.4 ATM Example

**Without Façade:** `ATM` class directly couples to `SecurityCodeChecker`, `FundChecker`, `AccountNumberChecker`, `Account`, `Transaction`, `CardReader`, `CashValidator` — high fan-out coupling.

**With Façade:** `ATM` couples only to `BankAccountFacade`; `BankAccountFacade` internally holds and coordinates all subsystem objects.

> [!info] Image: Slide 15 — class diagram showing `ATM` with arrows fanning out to all subsystem classes directly (without Facade)

![[Pasted image 20260613181929.png]]

> [!info] Image: Slide 16 — class diagram showing `ATM` → `BankAccountFacade` → subsystem classes (with Facade)

![[Pasted image 20260613181951.png]]
 
>[!example] `BankAccountFacade` structure:
> ```java
> public class BankAccountFacade {
>     private int accountNumber;
>     private int securityCode;
>     private AccountNumberCheck acctChecker;
>     private SecurityCodeCheck codeChecker;
>     private FundsCheck fundChecker;
>     private Account account;
>     private Transaction transaction;
>
>     public void withdrawCash(double cashToGet) { /* check acct, code, funds; decrement */ }
>     public void depositCash(double cashToDeposit) { /* check acct, code; increment */ }
> }
> ```

> [!summary] Key takeaways
> - Façade = single access point to a complex subsystem
> - Reduces coupling: clients depend on one class, not many
> - Implementation of the subsystem can change without affecting clients
> - Façades are typically Singletons

---

# 3. Decorator (GoF)

## 3.1 Problem and Solution

**Problem:** Add behaviour or state to _specific individual objects_ at run-time without changing the interface seen by clients. Inheritance is not feasible — it is static and applies to the entire class, not one instance.

**Solution:** **Encapsulate** the original concrete object (`ConcreteComponent`) inside an abstract wrapper interface (`Component`). Decorators (abstract class `Decorator`) also implement this interface and use **recursive composition** — each decorator wraps another `Component` (interface type), forwarding the core `operation()` call and adding its own behaviour.

- Category: _Structural_

## 3.2 General Form and Recursive Composition

Participants:

| Role | Description |
|---|---|
| `Component` | Abstract interface; declares `operation()` |
| `ConcreteComponent` | Core object clients always need; implements `operation()` |
| `Decorator` | Abstract; implements `Component`, holds a `- component : Component` reference |
| `ConcreteDecorator` | Extends `Decorator`; overrides `operation()` to add behaviour, then delegates to `component.operation()` |

- Recursive composition: a `ConcreteDecorator` wraps any `Component` — which may itself be another `ConcreteDecorator`
- Allows unlimited decorator layers; client interface never changes

> [!info] Image: Slide 25 — class diagram showing `Component` (abstract, `+ operation()`) with two subclasses: `ConcreteComponent` and `Decorator`. `Decorator` has a `- component` aggregation back to `Component` (the recursive link). `ConcreteDecorator` extends `Decorator`.

![[Pasted image 20260614181242.png]]

> [!note] A client always needs `ConcreteComponent.operation()` but may or may not want any `ConcreteDecorator.operation()` — decorators are optional add-ons

## 3.3 Collaboration and Sequencing

- `:Client` sends `operation()` to `:Decorator1`
- `:Decorator1` forwards `operation()` to `:Decorator2`
- `:Decorator2` forwards `operation()` to `:Component`, which executes it
- On the way back, each decorator runs its `addBehaviour()` before returning to the caller

> [!info] Image: Slide 28 — class diagram (left: `Client → Component ← Decorator ← Decorator1/Decorator2`) alongside a sequence diagram (right: `:Client → :Decorator1 → :Decorator2 → :Component`, with `addBehaviour()` calls on the return path)

## 3.4 Shape Decorator Example

**Context:** Drawing a shape is always required; adding a coloured border or fill is optional at run-time.

**Roles:**
- `Shape` (interface) → `Component`
- `Rectangle` → `ConcreteComponent`
- `ShapeDecorator` (abstract class implements `Shape`) → `Decorator`
- `RedShapeDecorator`, `BlueShapeDecorator` → `ConcreteDecorators`

> [!info] Image: Slide 30 — class diagram showing `Client → Component ← Decorator` hierarchy; sequence diagram (right) for `:Client → :RedShapeDecorator → :BlueShapeDecorator → :Rectangle`, with `setRedBorder()` and `setBlueFill()` on return

> [!example] `Rectangle.java` — `ConcreteComponent`:
> ```java
> public class Rectangle implements Shape {
>     @Override
>     public void draw() { System.out.println("Shape: Rectangle"); }
> }
> ```

> [!example] `ShapeDecorator.java` — abstract `Decorator`:
> ```java
> public abstract class ShapeDecorator implements Shape {
>     protected Shape decoratedShape;
>     public ShapeDecorator(Shape decoratedShape) {
>         this.decoratedShape = decoratedShape;
>     }
>     public void draw() { decoratedShape.draw(); }
> }
> ```

> [!example] `RedShapeDecorator.java` — `ConcreteDecorator`:
> ```java
> public class RedShapeDecorator extends ShapeDecorator {
>     public RedShapeDecorator(Shape decoratedShape) { super(decoratedShape); }
>     @Override
>     public void draw() {
>         decoratedShape.draw();   // draws the underlying Rectangle
>         setRedBorder(decoratedShape);
>     }
>     private void setRedBorder(Shape decoratedShape) {
>         System.out.println("Border Color: Red");
>     }
> }
> ```

> [!summary] Key takeaways
> - Decorator adds behaviour to individual objects at run-time, not at compile time
> - Client interface stays the same regardless of how many decorators are stacked
> - Relies on recursive composition — each layer wraps the previous
> - Alternative to subclassing for extending single instances

---

# 4. Builder (GoF)

## 4.1 Problem and Solution

**Problem:** A class needs to create different representations of a complex object (many parts, many configurations). Having creation logic inside the class bloats it; using a single constructor is unwieldy.

**Solution:** Encapsulate creation and assembly of a complex object in a separate **Builder** object. A class (**Director**) delegates object creation to the builder instead of doing it directly. The same `Director` can use different concrete builders to produce different representations.

- Category: _Creational_

## 4.2 Generic Structure and Sequence

Participants:

| Role | Responsibility |
|---|---|
| `Client` | Requests a widget from the `Director` |
| `Director` | Knows the construction _process_; creates the appropriate `ConcreteBuilder` and calls `buildWidget()` |
| `AbstractBuilder` | Defines `buildPartA()`, `buildPartB()`, `buildPartC()` |
| `ConcreteBuilder` | Implements each `buildPart` method with type-specific logic |
| `Widget` (Product) | The complex object being assembled |

**Sequence:**
- **Step 1** `Client` sends `getWidget("One")` to `Director`
- **Step 2** `Director` creates `:ConcreteBuilderOne`
- **Step 3** `Director` calls `buildWidget()` on the builder
- **Step 4** Builder creates `:Widget`, then calls `buildPartA()`, `buildPartB()`, `buildPartC()` on itself
- **Step 5** Completed `Widget` returned to `Client`

> [!info] Image: Slide 38 — sequence diagram showing `:Client → :Director → :ConcreteBuilderOne → :Widget`; `buildPartA/B/C` labelled as "These methods will build/set elements of the widget"

> [!note] `ConcreteBuilderOne` is one specialisation of `AbstractBuilder`; the `AbstractBuilder` defines the _build order_ (the template), concrete builders define _what_ each step does

## 4.3 Juice Example

**Context:** A juice shop must make different juices (orange, apple) using the same generic construction process: set type → set water → set fruit → set sugar.

**Class structure:**
- `BuilderClient` → calls `ShopKeeper.takeOrder(juiceType)`
- `ShopKeeper` (Director) → creates appropriate `JuiceMaker` and calls `makeJuice()`
- `JuiceMaker` (AbstractBuilder, abstract class) → defines `final makeJuice()` using `setJuiceType()`, `setWater()`, `setFruit()`, `setSugar()`
- `OrangeJuiceMaker`, `AppleJuiceMaker` (ConcreteBuilders) → implement each `set` method
- `Juice` (Product) → attributes: `+ fruit : int`, `- water : int`, `- sugar : int`

> [!info] Image: Slide 39 — class diagram with `BuilderClient → ShopKeeper → JuiceMaker → Juice`; `OrangeJuiceMaker` and `AppleJuiceMaker` extend `JuiceMaker`

> [!example] `makeJuice()` in `JuiceMaker` (AbstractBuilder):
> ```java
> abstract class JuiceMaker {
>     public final Juice makeJuice() {
>         Juice juice = createJuice();
>         setJuice(juice);
>         setJuiceType();
>         setWater();
>         setFruit();
>         setSugar();
>         return juice;
>     }
> }
> ```
> `makeJuice` is `final` — construction order is fixed in the abstract class. `OrangeJuiceMaker` and `AppleJuiceMaker` only override the individual `set` methods.

> [!info] Image: Slide 40 — sequence diagram "Ordering an Orange Juice": `:BuilderClient → :ShopKeeper → :OrangeJuice → :Juice`, showing `setJuiceType("orange")`, `setWater(40)`, `setFruit(4)`, `setSugar(10)` calls

> [!summary] Key takeaways
> - Builder separates _how_ an object is constructed from _what_ it produces
> - Director knows construction order; ConcreteBuilder knows part-specific logic
> - Same Director + different ConcreteBuilders = different product representations
> - Hides internal product representation from the client
