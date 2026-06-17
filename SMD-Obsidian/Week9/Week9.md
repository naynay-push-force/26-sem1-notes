# Key Concepts Checklist

-  [ ] 1. Strategy Pattern
	-  [ ] 1.1 Problem and solution
	-  [ ] 1.2 Context object and visibility
	-  [ ] 1.3 Strategy in collaboration (sequence diagram)
	-  [ ] 1.4 Code example
-  [ ] 2. Composite Pattern
	-  [ ] 2.1 Problem and solution
	-  [ ] 2.2 Generalised structure (Component / Leaf / Composite)
	-  [ ] 2.3 Example: composite shapes
	-  [ ] 2.4 Example: composite characters (DisneyMadness)
-  [ ] 3. Composite Strategy (POS Pricing)
	-  [ ] 3.1 Problem 2.1 — complex pricing logic
	-  [ ] 3.2 Problem 2.2 — multiple conflicting policies
	-  [ ] 3.3 Conflict resolution strategies
	-  [ ] 3.4 Composite pricing strategy design
	-  [ ] 3.5 Collaboration with a composite
	-  [ ] 3.6 Creating a composite strategy (sequence diagram)
	-  [ ] 3.7 Customer discount extension flow
	-  [ ] 3.8 Design reasoning summary
-  [ ] 4. Template Method Pattern
	-  [ ] 4.1 Problem and solution
	-  [ ] 4.2 Template method definition
	-  [ ] 4.3 Example: CaffeineBeverage (Tea/Coffee)
-  [ ] 5. Observer Pattern
	-  [ ] 5.1 Problem and solution
	-  [ ] 5.2 Generalised structure (Subject / Observer)
	-  [ ] 5.3 POS example: refreshing sales total UI
-  [ ] 6. Pattern Comparisons
	-  [ ] 6.1 Adapter vs Decorator
	-  [ ] 6.2 Composite vs Decorator
	-  [ ] 6.3 Decorator vs Strategy
	-  [ ] 6.4 Template Method vs Strategy

---

# 1. Strategy Pattern

## 1.1 Problem and Solution

**Problem:** How to design for varying but related algorithms or policies? How to allow these to change at runtime?

**Solution:** Define each algorithm/policy/strategy in a separate class with a common interface.

## 1.2 Context Object and Visibility

- The **context object** holds a reference to its strategy via attribute visibility
- `Sale` maintains a `pricingStrategy` attribute of type `ISalePricingStrategy`
- `Sale.getTotal()` delegates to `pricingStrategy.getTotal(this)` — passes `this` so the strategy has parameter visibility to the `Sale`

> [!info] Image: DCD showing Sale → ISalePricingStrategy (attribute visibility), with PercentDiscountPricingStrategy and AbsoluteDiscountOverThresholdPricingStrategy implementing it — slide 7

![[Pasted image 20260615153505.png]]

## 1.3 Strategy in Collaboration

- `s:Sale` receives `t = getTotal`
- Inside a loop: calls `st = getSubtotal` on each `lineItems[i]:SalesLineItem`
- After loop: calls `t = getTotal(s)` on `:PercentDiscountPricingStrategy`
- Strategy calls `pdt = getPreDiscountTotal` back on `Sale` (parameter visibility)
- Computes `{ t = pdt * percentage }`

> [!info] Image: Sequence diagram showing Sale → SalesLineItem loop, then Sale passes itself to PercentDiscountPricingStrategy — slide 8

![[Pasted image 20260615153832.png]]

![[Pasted image 20260615153630.png]]

> [!note] The `Sale` is passed as a parameter to the strategy so the strategy can call back on it (`getPreDiscountTotal`). This is parameter visibility.

## 1.4 Code Example

```java
public interface ISalePricingStrategy {
    public double getTotal(Sale s);
}

public class PercentDiscountPricingStrategy implements ISalePricingStrategy {
    private double percentage = 0.05;
    public double getTotal(Sale s) {
        return s.getPreDiscountTotal() - (s.getPreDiscountTotal() * percentage);
    }
}

public class AbsoluteDiscountOverThresholdPricingStrategy implements ISalePricingStrategy {
    private double threshold = 50;
    private double discount = 5;
    public double getTotal(Sale s) {
        double pdt = s.getPreDiscountTotal();
        if (pdt >= threshold) { return pdt - discount; }
        else { return pdt; }
    }
}
```

> [!summary] Key takeaways
> - Each algorithm lives in its own class behind a common interface (`ISalePricingStrategy`)
> - Context (`Sale`) delegates to the strategy, passing itself for callback access
> - New pricing policies add new classes without modifying `Sale`

---

# 2. Composite Pattern

## 2.1 Problem and Solution

**Problem:** How to treat a group/composition structure of objects the same way (polymorphically) as a non-composite (atomic) object?

**Solution:** Define classes for composite and atomic objects so they implement the same interface.

## 2.2 Generalised Structure

Three roles:

| Role | Description |
|---|---|
| **Component** | Common interface/abstract class; declares `operation()` |
| **Leaf** | Atomic object; implements `operation()` |
| **Composite** | Contains 0..* `Component` children; implements `operation()` by delegating to children; also has `add()`, `remove()`, `getChild()` |

> [!info] Image: Class diagram — Component (abstract) with self-association 0..* child; Leaf and Composite both extend Component; Composite has aggregation back to Component (parent) — slide 14

> [!note] UML notation: abstract classes and abstract methods shown in *italics*.

## 2.3 Example: Composite Shapes

- Abstract class `Shape` with abstract `draw(g:Graphic)`
- Leaf subclasses: `Circle` (radius, center), `Line` (start, end), `Rectangle` (upperLeft, lowerRight)
- Composite subclass: `Picture` — holds `ArrayList<Shape>`; `draw()` iterates and calls `s.draw()` on each child
- `Picture.addShape(s:Shape)` adds children

```java
public class Picture extends Shape {
    ArrayList<Shape> shapes;
    public void addShape(Shape shape) { shapes.add(shape); }
    public void draw() {
        for (Shape s : shapes) { s.draw(); }
    }
}
```

> [!example] `myPicture.draw()` calls `Circle.draw()`, `Rectangle.draw()`, `Line.draw()` transparently — caller doesn't know or care if it's dealing with a leaf or composite.

> [!info] Image: DCD showing Shape (abstract, italicised name) with self-association 0..n; Circle, Line, Rectangle, Picture all extend Shape; Picture has `addShape(s:Shape)` — slide 15

## 2.4 Example: Composite Characters (DisneyMadness)

- Existing design: `IDisneyAdapter` interface with `move():void`; implemented by `SlothAdapter`, `IronManAdapter`, `TrooperAdapter`
- To support a group (Disney Army): add `Army` class that also implements `IDisneyAdapter`
- `Army` holds a collection of `IDisneyAdapter` objects (0..*); its `move()` delegates to each member
- `IDisneyAdapter` self-association (0..*) is the Composite pattern applied

> [!info] Image: Updated DCD with Army added as a fourth implementor of IDisneyAdapter, with IDisneyAdapter having a 0..* self-aggregation — slide 20

> [!summary] Key takeaways
> - Composite and Leaf share an interface — callers treat them identically
> - Composite delegates `operation()` to its children
> - Adding new composites (e.g. `Army`) does not require changing the interface or existing leaves

---

# 3. Composite Strategy (POS Pricing)

## 3.1 Problem 2.1 — Complex Pricing Logic

POS needs to support varying, swappable pricing strategies, e.g., 10% off all sales one week, $10 off purchases over $200 the next.

## 3.2 Problem 2.2 — Multiple Conflicting Policies

Multiple policies may apply simultaneously, e.g. on a Monday:
- 20% senior discount
- 15% preferred customer discount (sales > $400)
- $50 off purchases over $500 (Monday only)
- 15% off everything if customer buys 1 case Darjeeling tea

Variation factors: customer type, time period, line item product.

## 3.3 Conflict Resolution Strategies

- Some discounts **cannot be combined**
- Two resolution approaches: **Best for Customer** (minimum total) or **Best for Store** (maximum total)
- Need: determine which strategies apply → apply conflict resolution

## 3.4 Composite Pricing Strategy Design

```
> [!info] Image: Full DCD — ISalePricingStrategy interface; PercentageDiscountPricingStrategy and AbsoluteDiscountOverThresholdPricingStrategy as leaves; abstract CompositePricingStrategy implements ISalePricingStrategy and holds 1..* strategies (List<ISalePricingStrategy>); CompositeBestForCustomerPricingStrategy and CompositeBestForStorePricingStrategy extend it; Sale holds one ISalePricingStrategy — slide 22–24
```

Key structural points:
- `CompositePricingStrategy` is an **abstract class** (shown in italics in DCD)
- It holds `protected ArrayList<ISalePricingStrategy> pricingStrategies`
- `add(ISalePricingStrategy)` is concrete; `getTotal(Sale)` is abstract
- `Sale` still holds a single `pricingStrategy : ISalePricingStrategy` — unaware of whether it's composite or leaf (Strategy pattern still applies)

```java
abstract class CompositePricingStrategy implements ISalePricingStrategy {
    protected ArrayList<ISalePricingStrategy> pricingStrategies = new ArrayList<>();
    public void add(ISalePricingStrategy strategy) { pricingStrategies.add(strategy); }
    public abstract double getTotal(Sale s);
}

public class CompositeBestForCustomerPricingStrategy extends CompositePricingStrategy {
    public double getTotal(Sale s) {
        double lowestTotal = s.getPreDiscountTotal();
        for (ISalePricingStrategy strat : this.pricingStrategies) {
            double total = strat.getTotal(s);
            if (lowestTotal > total) { lowestTotal = total; }
        }
        return lowestTotal;
    }
}
```

## 3.5 Collaboration with a Composite

- `Sale.getTotal()` calls `pricingStrategy.getTotal(this)` — same as atomic case
- Internally, `CompositeBestForCustomerPricingStrategy` loops over `strategies[j]:ISalePricingStrategy`, calling `x = getTotal(s)` on each
- Resolves: `{ t = min(set of all x) }`

> [!info] Image: Sequence diagram — s:Sale → CompositeBestForCustomerPricingStrategy → loop over strategies[j]:ISalePricingStrategy — slide 25

> [!note] UML 2: `strategies[j] : :ISalePricingStrategy` denotes an object of unknown class that implements the interface — interface used as type in lifeline header.

## 3.6 Creating a Composite Strategy (Factory)

```java
public class PricingStrategyFactory {
    public ISalePricingStrategy getCompositeSalePricingStrategy() {
        switch(today.getDayOfWeek()) {
            case MONDAY:
                CompositePricingStrategy cs = new CompositeBestForCustomerPricingStrategy();
                cs.add(new PercentDiscountPricingStrategy());
                cs.add(new TeaDiscountPricingStrategy());
                return cs;
            case FRIDAY:
                return new AbsoluteDiscountOverThresholdPricingStrategy();
        }
    }
}
```

> [!info] Image: Sequence diagram for makeNewSale — Register creates Sale, Sale calls getSalePricingStrategy on PricingStrategyFactory, Factory creates CompositeBestForCustomerPricingStrategy and adds a PercentageDiscountPricingStrategy — slide 26

## 3.7 Customer Discount Extension Flow

Alternative flow: customer claims a discount (employee, preferred customer).
1. Cashier signals discount request, enters customer ID
2. `Register` calls `getCustomer(custID)` on `Store`
3. `Register` calls `enterCustomerForDiscount(c:Customer)` on `Sale`
4. `Sale` calls `addCustomerPricingStrategy(s)` on `PricingStrategyFactory` (passes aggregate `Sale` as parameter)
5. Factory retrieves existing composite strategy, calls `getCustomerPercentage(c)`, creates a `PercentageDiscountPricingStrategy`, and `add(cs)` to the composite

> [!info] Image: Two sequence diagrams — (1) Register → Store (IDs to Objects GRASP), (2) "Enter Customer For Discount" sd — Sale → PricingStrategyFactory → CompositeBestForCustomerPricingStrategy → PercentageDiscountPricingStrategy — slides 31–32

Rationale annotations on diagram:
- `Register` as controller — by Controller
- `Store` resolves custID → Customer — by Expert and IDs to Objects
- `Sale` handles own discount — by Expert
- `PricingStrategyFactory` manages strategy creation — by Factory and High Cohesion
- Composite used to `add` new strategy — by Factory and Composite

## 3.8 Design Reasoning Summary

Combined patterns and principles used for POS complex pricing:

| Principle/Pattern | Role |
|---|---|
| Protected Variation | Shield `Sale` from varying pricing algorithms |
| Polymorphism | `ISalePricingStrategy` allows uniform invocation |
| High Cohesion | `PricingStrategyFactory` owns strategy creation logic |
| Low Coupling | `Sale` knows only `ISalePricingStrategy` interface |
| Strategy | Each pricing algorithm is a separate class |
| Composite | Group multiple strategies, treat as one |
| Factory | Centralise strategy instantiation |
| Singleton | One `PricingStrategyFactory` instance |

> [!summary] Key takeaways
> - Composite Strategy = Strategy Pattern + Composite Pattern
> - `Sale` remains unchanged whether it uses a leaf or composite strategy
> - Factory creates and assembles the composite; `Sale` calls `pricingStrategy.getTotal(this)` regardless

---

# 4. Template Method Pattern

## 4.1 Problem and Solution

**Problem:** How to build generic components that are easy to extend without replicating code?

**Solution:** Define a family of algorithms, encapsulate each, make them interchangeable. Implement invariant parts once in a superclass; let subclasses implement the varying parts.

## 4.2 Template Method Definition

A **template method** defines an algorithm as a set of steps. One or more steps are declared abstract and implemented by subclasses. The algorithm's structure stays unchanged; subclasses provide parts of the implementation.

- **Hollywood Principle:** "Don't call us, we'll call you" — the superclass template method calls subclass methods, inverting the usual direction.

> [!note] Template Method uses **Inheritance**. Strategy uses **Delegation**. Strategy does not require a structured step-by-step process like Template Method does.

## 4.3 Example: CaffeineBeverage (Tea & Coffee)

Both Tea and Coffee share: boil water → brew/steep → pour → add condiment. Structure is identical; only brew and condiment steps differ.

> [!info] Image: DCD — abstract CaffeineBeverage with prepareRecipe() (abstract), boilWater(), pourInCup(); Coffee subclass with prepareRecipe(), brewCoffeeGrinds(), addSugarAndMilk(); Tea subclass with prepareRecipe(), steepTeaBag(), addLemon() — slide 37

- `boilWater()` and `pourInCup()` — shared, defined in superclass
- `prepareRecipe()` — abstract in superclass, each subclass implements its own
- `brewCoffeeGrinds()`, `addSugarAndMilk()`, `steepTeaBag()`, `addLemon()` — stay in subclasses

> [!summary] Key takeaways
> - Template method locks the algorithm skeleton in the superclass
> - Subclasses fill in the variant steps without changing the overall structure
> - Prevents code duplication when algorithms share the same sequence of steps

---

# 5. Observer Pattern

## 5.1 Problem and Solution

**Problem:** Different subscriber objects want to react to state changes/events from a publisher object in their own way, while the publisher maintains low coupling to subscribers.

**Solution:** Define a subscriber/listener interface. Subscribers implement it. Publisher dynamically registers subscribers and notifies them on events via `update()`.

## 5.2 Generalised Structure

| Role | Class | Responsibilities |
|---|---|---|
| Publisher (Subject) | `SubjectBase` (abstract) | Holds `observers:ObserverBase List`; `addObserver()`, `removeObserver()`, `update()`/`publish()` |
| Subscriber (Observer) | `ObserverBase` (abstract) | Declares `update()` |
| Concrete Subject | `ConcreteSubject` | Business logic; notifies observers on change |
| Concrete Observer | `ConcreteObserver` | Holds reference back to `ConcreteSubject`; implements `update(subject:SubjectBase)` |

> [!info] Image: DCD — abstract SubjectBase ← ConcreteSubject (a.k.a Publisher); abstract ObserverBase ← ConcreteObserver (a.k.a Listener/Subscriber); ConcreteSubject ↔ ConcreteObserver; SubjectBase → ObserverBase (dependency) — slide 40

> [!note] `SubjectBase` is **not** necessary if only one concrete subject is needed. There can be multiple `ConcreteSubject` and `ConcreteObserver` classes.

## 5.3 POS Example: Refreshing Sales Total UI

- Goal: GUI window shows current sale total; must refresh when `Sale.total` changes
- **Problem:** `Sale` should **not** hold a reference to the UI window — violates Low Coupling (domain → UI dependency)
- **Solution:** GUI window registers as an observer of `Sale`; when `Sale.setTotal(newTotal)` is called, it notifies all observers

> [!summary] Key takeaways
> - Observer decouples publisher from subscriber — publisher only knows `ObserverBase` interface
> - Supports multiple independent observers reacting differently to the same event
> - Fits MVC/layered architecture: domain objects don't depend on UI classes

---

# 6. Pattern Comparisons

| Comparison | Detail |
|---|---|
| **Adapter** vs **Decorator** | Adapter provides a *different* interface; Decorator provides an *enhanced* interface |
| **Composite** vs **Decorator** | Both use recursive composition; Composite is about *representation* (tree structures); Decorator is about *embellishment* (adding behaviour) — often used together |
| **Decorator** vs **Strategy** | Decorator changes the *skin* (wraps object, adds behaviour); Strategy changes the *guts* (replaces the algorithm) |
| **Template Method** vs **Strategy** | Both separate generic algorithm from detail; Template uses *Inheritance*; Strategy uses *Delegation* |

> [!note] Exam gotcha: Strategy and Adapter **cannot** be used interchangeably — they solve different problems (varying algorithms vs. incompatible interfaces). Both may use polymorphism, but their intent and structure differ.
