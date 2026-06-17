# Key Concepts Checklist

- [ ] 1. Adapter Pattern
    - [ ] 1.1 Problem and definition
    - [ ] 1.2 Structure — interfaces and adapter classes
    - [ ] 1.3 Disney Madness example (without vs. with Adapter)
    - [ ] 1.4 Implementation in Java
    - [ ] 1.5 Adapter in relation to GRASP principles
    - [ ] 1.6 Issues arising — who creates adapters?
- [ ] 2. (Concrete) Factory Pattern
    - [ ] 2.1 Problem and definition
    - [ ] 2.2 Advantages of Factory
    - [ ] 2.3 ServicesFactory structure and lazy creation
    - [ ] 2.4 DisneyFactory implementation
    - [ ] 2.5 Issues arising — who creates the Factory?
- [ ] 3. Singleton Pattern
    - [ ] 3.1 Problem and definition
    - [ ] 3.2 Implementation — static method and lazy initialisation
    - [ ] 3.3 UML notation for Singleton
    - [ ] 3.4 Why not make all service methods static?
    - [ ] 3.5 Accessing the Singleton Factory
- [ ] 4. Patterns Working Together
    - [ ] 4.1 Full POS solution: Adapter + Factory + Singleton
    - [ ] 4.2 Design vocabulary — talking in patterns

---

# 1. Adapter Pattern

## 1.1 Problem and Definition

- **Problem:** How to resolve incompatible interfaces, or provide a stable interface to similar components with different interfaces?
- **Solution:** Convert the original interface of a component into another interface through an intermediate adapter object
- "Interface" here means API surface — _not_ a Java `interface` keyword

> [!info] Image: Slide 5 — power plug adapter photo illustrating the concept

**The Adapter Pattern** converts the interface of a class into another interface the client expects — lets classes work together that otherwise couldn't due to incompatible interfaces.

Two key benefits:

- Decouples the client from the interface implementation
- Interface changes are encapsulated within the adapter

## 1.2 Structure — Interfaces and Adapter Classes

- We define a stable interface (e.g. `ITaxCalculatorAdapter`) that the client uses
- For each external system, we create a concrete adapter that implements this interface
- Adapters use interfaces and polymorphism to add a level of indirection to varying APIs

> [!info] Image: Slide 7 — class diagram showing `ITaxCalculatorAdapter` with `TaxMasterAdapter` and `GoodAsGoldTaxProAdapter` implementing it; also `IAccountingAdapter` with `SAPAccountingAdapter` and `GreatNorthernAccountingAdapter`; plus `IInventoryAdapter` and `ICreditAuthorizationServiceAdapter`

POS example external services and adapters:

|Interface|Concrete Adapters|
|---|---|
|`ITaxCalculatorAdapter`|`TaxMasterAdapter`, `GoodAsGoldTaxProAdapter`|
|`IAccountingAdapter`|`SAPAccountingAdapter`, `GreatNorthernAccountingAdapter`|
|`IInventoryAdapter`|_(implementation-specific)_|
|`ICreditAuthorizationServiceAdapter`|_(implementation-specific)_|

- `getTaxes(Sale) : List of TaxLineItems` — method defined on `ITaxCalculatorAdapter`
- `postReceivable(CreditPayment)`, `postSale(Sale)` — methods on `IAccountingAdapter`

## 1.3 Disney Madness Example

**Without Adapter**

- `DisneyMadness` must interact directly with `DisneyCharacters::Sloth`, `MavelHeroes::IronMan`, `LucasFilm::Trooper`
- High coupling — client knows all three incompatible APIs

> [!info] Image: Slide 10 — class diagram showing `DisneyMadness` with dashed dependency arrows to all three external classes

External APIs (read-only):

|Class|Methods|
|---|---|
|`DisneyCharacters::Sloth`|`+ wake(): void`, `+ pushToMove(): void`|
|`MavelHeroes::IronMan`|`+ fly(): void`|
|`LucasFilm::Trooper`|`+ control(string): void`|

**With Adapter**

- Define `IDisneyAdapter` interface with single method `move(): void`
- Each character gets a concrete adapter implementing `move()`
- `DisneyMadness` only knows `IDisneyAdapter` — zero knowledge of individual APIs

> [!info] Image: Slide 12 — class diagram with `IDisneyAdapter` at top; `SlothAdapter`, `IronManAdapter`, `TrooperAdapter` implementing it; each adapter using its respective external class

## 1.4 Implementation in Java

```java
public interface IDisneyAdapter {
    public void move();
}

public class SlothAdapter implements IDisneyAdapter {
    private Sloth theSloth;
    public SlothAdapter(String name) {
        theSloth = new Sloth(name);
    }
    public void move() {
        theSloth.wake();
        theSloth.pushToClimb();
    }
}
```

Client code with adapters:

```java
public class DisneyMadess {
    public static void main(String[] args) {
        IDisneyAdapter slothAdt = new SlothAdapter("Flash");
        slothAdt.move();
        IDisneyAdapter ironManAdt = new IronManAdapter("Tony");
        ironManAdt.move();
        IDisneyAdapter trooperAdt = new TrooperAdapter("Storm");
        trooperAdt.move();
    }
}
```

- Client calls `move()` on all — no knowledge of individual movement logic

## 1.5 Adapter in Relation to GRASP Principles

- **Adapter** is a kind of **Indirection** and **Pure Fabrication** that uses **Polymorphism**
- Chain of generalisation: Adapter → Indirection → Low Coupling → Protected Variation
- Adapter also contributes to **High Cohesion** via Pure Fabrication

> [!info] Image: Slide 16 — hierarchy diagram showing GRASP principles (Protected Variation, Low Coupling, High Cohesion, Polymorphism, Indirection, Pure Fabrication) with Adapter at the bottom crossing the GoF / GRASP boundary

> [!summary] Key takeaways
> 
> - Adapter resolves incompatible interfaces without modifying either component
> - One adapter per external system; one interface per service type
> - Decouples client from API — interface changes stay inside the adapter
> - GRASP mapping: Adapter = Pure Fabrication + Indirection + Polymorphism

## 1.6 Issues Arising — Who Creates Adapters?

- Should `Register` directly create `SAPAccountingAdapter`? No.
- Creating adapters is not pure application logic
- Adapter objects are not domain objects
- **Use GRASP Pure Fabrication** — fabricate a dedicated object to manage creation

---

# 2. (Concrete) Factory Pattern

## 2.1 Problem and Definition

- **Problem:** Who should be responsible for creating objects when there are special considerations — complex creation logic, desire to separate creation responsibilities for better cohesion?
- **Solution:** Create a **Pure Fabrication** object called a Factory that handles the creation
- Also called **Simple Factory** — simplified version of GoF Abstract Factory

## 2.2 Advantages of Factory

- Separates responsibility of complex creation into cohesive helper objects
- Hides potentially complex creation logic
- Allows performance-enhancing memory management strategies (e.g. object caching/recycling — useful when connecting to a database many times)

## 2.3 ServicesFactory Structure and Lazy Creation

> [!info] Image: Slide 22 — `ServicesFactory` class box with attributes `accountingAdapter`, `inventoryAdapter`, `taxCalculatorAdapter` (all typed to interfaces); methods `getAccountingAdapter()`, `getInventoryAdapter()`, `getTaxCalculatorAdapter()` returning interface types; code snippet for lazy creation; properties file entry

```
ServicesFactory
─────────────────────────────────────────
accountingAdapter : IAccountingAdapter
inventoryAdapter : IInventoryAdapter
taxCalculatorAdapter : ITaxCalculatorAdapter
─────────────────────────────────────────
getAccountingAdapter() : IAccountingAdapter
getInventoryAdapter() : IInventoryAdapter
getTaxCalculatorAdapter() : ITaxCalculatorAdapter
```

- Factory methods return objects typed to an **interface**, not a class — factory can return any implementation
- **Lazy creation** — don't create adapter unless required:

```java
if ( taxCalculatorAdapter == null ) {
    String className = System.getProperty( "taxcalculator.class.name" );
    taxCalculatorAdapter = (ITaxCalculatorAdapter) Class.forName( className ).newInstance();
}
return taxCalculatorAdapter;
```

- Class name read from a **properties file** at runtime:

```
taxcalculator.class.name=TaxMasterAdaptor
```

> [!note] What defines the Factory pattern: the ability to get the right adapter without the client knowing which concrete class it is. The selection mechanism (properties file, enum, method logic) can vary.

## 2.4 DisneyFactory Implementation

```java
public class DisneyFactory {
    private IDisneyAdapter characterAdapter = null;

    public IDisneyAdapter getDisneyAdapter(String character, String name) {
        if(character.equals("ironman")) {
            characterAdapter = (IDisneyAdapter) new IronManAdapter(name);
        } else if(character.equals("sloth")) {
            characterAdapter = (IDisneyAdapter) new SlothAdapter(name);
        } else if(character.equals("trooper")) {
            characterAdapter = (IDisneyAdapter) new TrooperAdapter(name);
        }
        return characterAdapter;
    }
}
```

- Could use an **Enum** instead of a String for the character parameter
- Could use a properties file instead of if-else logic

## 2.5 Issues Arising — Who Creates the Factory?

- Factory may need to be called from various places (e.g. `Register`)
- Only one instance of the factory is needed
- Passing the factory as a parameter to every method adds too much complexity
- **Solution:** Use the **Singleton pattern** for global, single-point access

---

# 3. Singleton Pattern

## 3.1 Problem and Definition

- **Problem:** Exactly one instance of a class is allowed. Objects need a global and single point of access.
- **Solution:** Define a static method of the class that returns the singleton

**The Singleton Pattern** ensures a class has only one instance, and provides a global point of access to it.

## 3.2 Implementation — Static Method and Lazy Initialisation

```java
// static method
public static synchronized ServicesFactory getInstance() {
    if ( instance == null )
        instance = new ServicesFactory();
    return instance;
}
```

- `synchronized` modifier — prevents race conditions in multi-threaded contexts
- **Lazy initialisation** — instance created only on first call

## 3.3 UML Notation for Singleton

> [!info] Image: Slide 28 — `ServicesFactory` class diagram with underlined `instance : ServicesFactory` (static attribute) and underlined `getInstance() : ServicesFactory` (static method); note that `1` near the class box indicates singleton

- **Underlined** attribute or method in a class box = static (class-level) member
- `1` near the class box = optionally indicates only one instance will be created

## 3.4 Why Not Make All Service Methods Static?

Three reasons only `getInstance()` is static and not e.g. `getAccountingAdapter()`:

- **Polymorphism:** static methods are not polymorphic (virtual) — can't be overridden in subclasses
- **Remote access:** most RMI-style communication mechanisms don't support remote-enabling of static methods
- **Context-dependence:** a class is not always a singleton in all application contexts

## 3.5 Accessing the Singleton Factory

```java
public class Register {
    public void initialize() {
        accountingAdapter =
            ServicesFactory.getInstance().getAccountingAdapter();
    }
}
```

- `ServicesFactory.getInstance()` is globally visible — any class can call it
- UML sequence diagram notation: a `1` near the lifebox of `:ServicesFactory` indicates visibility was obtained via Singleton

> [!info] Image: Slide 30 — sequence diagram with `:Register` sending `aa = getAccountingAdapter` to `:ServicesFactory` (annotated with `1` indicating Singleton access)

> [!summary] Key takeaways
> 
> - Singleton: private constructor + static `instance` attribute + static `getInstance()` method
> - Use `synchronized` to be thread-safe
> - Only `getInstance()` is static — all service methods remain instance methods for polymorphism support
> - Lazy initialisation avoids creating the object until first use

---

# 4. Patterns Working Together

## 4.1 Full POS Solution: Adapter + Factory + Singleton

Complete solution to the "external services with varying interfaces" problem:

> [!info] Image: Slide 33 — two-part sequence diagram: (top) Creating the Solution — `:Store` creates `:Register`; `:Register` calls `getAccountingAdapter` on `:ServicesFactory` (Singleton); factory creates `:SAPAccountingAdapter`; (bottom) Using the Solution — `:Register` calls `makePayment`, creates `:Payment`, calls `postSale(sale)` on `accountingAdapter:SAPAccountingAdapter` which communicates via SOAP over HTTP to `:SAPSystem`

**Creating the solution:**

1. `:Store` creates `:Register`
2. `:Register` calls `ServicesFactory.getInstance().getAccountingAdapter()`
3. `ServicesFactory` lazily creates and returns `:SAPAccountingAdapter`

**Using the solution:**

1. `:Register` receives `makePayment`
2. Creates `:Payment`
3. Calls `postSale(sale)` on the adapter
4. Adapter translates to SOAP over HTTP → `:SAPSystem`

## 4.2 Design Vocabulary — Talking in Patterns

The full solution can be described in one sentence: _"Use Adapters generated from a Singleton Factory."_

Design reasoning draws on all of: Controller, Creator, Protected Variations, Low Coupling, High Cohesion, Indirection, Polymorphism, Adapter, Factory, Singleton.

> [!note] GRASP principles generalise GoF patterns — GoF patterns are specific, named examples of GRASP principles in action.

> [!summary] Key takeaways
> 
> - Adapter wraps incompatible external APIs behind a stable interface
> - Factory (Pure Fabrication) handles creation of the right adapter — keeps `Register` cohesive
> - Singleton gives global access to the one Factory instance without parameter passing
> - The three patterns chain naturally: Adapter solves interface mismatch → Factory solves creation → Singleton solves access