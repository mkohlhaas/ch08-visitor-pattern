### The Visitor Design Pattern

In Rust, the Visitor design pattern is a behavioral pattern used to separate an
algorithm from the heterogeneous data structure it operates on, allowing you to
add new operations without modifying the underlying data structures. While
traditional Object-Oriented Programming (OOP) relies on virtual method tables
and double-dispatch (Element::accept(Visitor)), Rust developers often prefer
Enums and Pattern Matching as a native, highly efficient alternative.

However, the classic trait-based Visitor pattern remains highly relevant in
Rust for complex tree traversals, compiler Abstract Syntax Trees (ASTs), and
industry-standard libraries like [Serde](https://refactoring.guru/design-patterns/visitor/rust/example).

### Real-World Usage: The Serde Framework

The most prominent real-world manifestation of this pattern in the Rust
ecosystem is Serde (the standard serialization/deserialization framework).

When deserializing data, Serde constructs a Visitor type that tells the parser
exactly how to map unstructured keys and values into structured memory without
the parser needing to know what final Rust struct it is assembling.

### UML Diagrams

#### Class Diagram

```
┌──────────────────────────────┐      ┌──────────────────────────────┐
│         «interface»          │      │        «interface»           │
│          Visitor             │      │           Shape              │
│──────────────────────────────│      │        (Visitable)           │
│ + visit_circle(&mut self,    │      │──────────────────────────────│
│     circle: &Circle)         │      │ + accept(&self, visitor:     │
│ + visit_rectangle(&mut self, │      │     &mut dyn Visitor)        │
│     rectangle: &Rectangle)   │      └──────────────┬───────────────┘
└──────────────┬───────────────┘                     │ implements
               │ ▲                                   │
               │ │ «implements»                      ▼
┌──────────────┴───────────────┐      ┌──────────────────────────────┐
│       TotalAreaCalculator    │      │              ^               │
│      (ConcreteVisitor)       │      │              │               │
│──────────────────────────────│      │              └───_..._       │
│ + total_area: f64            │   ┌────────────┐              ┌────────────┐
│──────────────────────────────│   │   Circle   │              │ Rectangle  │
│ + new()                      │   │────────────│              │────────────│
│ + visit_circle(...)          │   │ + radius:  │              │ + width:   │
│ + visit_rectangle(...)       │   │   f64      │              │   f64      │
└──────────────────────────────┘   │            │              │ + height:  │
                                   │            │              │   f64      │
                                   └────────────┘              └────────────┘
```

#### Double-Dispatch Flow

```
   1st dispatch              2nd dispatch
┌────────────────────────┐        ┌──────────────────────────────┐
│ shape.accept(&visitor) │  ───▶ │ visitor.visit_circle(circle) │
└────────────────────────┘        └──────────────────────────────┘
(Shape calls Visitor back)        (visitor calls back on the concrete type)
```

In Rust's trait form: `Shape` holds an `accept(&mut dyn Visitor)`; each concrete
type (`Circle`, `Rectangle`) re-dispatches to the matching `visit_*` method — the
"double dispatch."

### Summary Comparison

| Feature | Enums & Pattern Matching | Classic Trait Visitor |
|---|---|---|
| Performance | Faster (Static dispatch / no VTables) | Slower (Dynamic dispatch via dyn) |
| Extensibility | Easy to add new operations; hard to add data variants | Easy to add new visitors; hard to add data variants |
| Boilerplate | Low | High (accept/visit structures) |
| Best Used For | General application logic, internal APIs | ASTs, compilers, library deserializers |
