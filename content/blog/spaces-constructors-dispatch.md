+++
date = '2026-04-21T10:00:00-03:00'
draft = false
title = 'Spaces, Constructors, and Dispatch'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "future-of-code", "programming-languages", "howto", "LLM"]
+++

Previous posts covered how data flows through graphs and how closures provide control flow. This post looks at how LQ organizes code: where methods live, how objects are constructed, how the right method gets called, and how LQ simulates traditional object-oriented dispatch without building it into the language.

## Spaces

A **space** is a file containing a set of related methods. Think of it as a module or namespace — a way to group methods that belong together thematically.

A space is not a class. There are no properties. No inheritance hierarchy. No `public` or `private` keywords. A space is simply an organizational unit — a container for methods that share a common theme.

A space named `Geometry` might contain methods for working with points, lines, and shapes. A space named `JSON` might contain `readJSON`, `writeJSON`, and related utilities. The grouping is for humans (and agents) reading the code. The compiler treats methods across all spaces uniformly.

This is a deliberate choice. Classes conflate two concerns: organizing code and defining types. LQ separates them. Spaces organize code. Types are defined by their structure — their properties and the methods that operate on them. The two are independent.

## Constructors

Objects in LQ are created through constructors. A constructor is a regular method — no special syntax, no `new` keyword — that contains a **pack** node.

The method's name *is* the type name. A method called `Point` that packs `x` and `y` values creates an instance of type `Point`.

<figure style="text-align: center;">
    <img alt="Point constructor method with pack node" width="25%" src="/blog/images/tutorial4.1.png">
    <figcaption><code>Point</code> constructor — a method containing a <code>pack</code> node</figcaption>
</figure>

The `pack` node takes named inputs — the properties of the type — and produces a single output: an instance of the enclosing method's type. In this case, `pack` receives `x` and `y` and emits a `Point`.

```python
class Point:
    def __init__(self, x, y):
        self.x = x
        self.y = y

p = Point(3, 4)
```

In LQ, the `Point` method receives `x` and `y` as input parameters, wires them into the `pack` node, and outputs the constructed `Point`. Calling `Point(3, 4)` as a node in another graph produces a `Point` instance.

There's no distinction between a constructor and any other method. A constructor is just a method that happens to contain a `pack` node. This means constructors can contain arbitrary logic — validation, derived values, conditional initialization — using the same nodes and closures as any other method.

To access properties from a packed instance, the corresponding **unpack** node extracts all named values at once. Wire a `Point` into an `unpack` node and the `x` and `y` values flow out through separate output terminals. In this example, we add two point together.

<figure style="text-align: center;">
    <img alt="unpack node extracting properties from a Point instance" width="50%" src="/blog/images/tutorial4.2.png">
    <figcaption><code>unpack</code> — extracting properties from a <code>Point</code></figcaption>
</figure>

### Properties, Getters, and Withers

The named values inside a packed instance are **properties**. Individual properties are accessed through **getter** nodes — a node labeled with the property name that takes the instance as input and outputs the property value. Taking the previous example and replacing unpack with specific getters.

<figure style="text-align: center;">
    <img alt="getter nodes extracting x and y from a Point" width="50%" src="/blog/images/tutorial4.3.png">
    <figcaption>getter nodes — accessing individual properties</figcaption>
</figure>

Setting a property works differently than in most languages. Instances in LQ are **immutable** — once packed, the values inside cannot be changed. Instead, LQ uses **withers**: a setter that creates a *new* instance with the updated value, leaving the original untouched.

<figure style="text-align: center;">
    <img alt="wither node creating a new Point with a different x value" width="40%" src="/blog/images/tutorial4.4.png">
    <figcaption>wither — creates a new <code>Point</code> with the updated property</figcaption>
</figure>

A wither on a `Point` that sets `x` to 10.0 produces a new `Point` with `x = 10.0` and the original `y` value carried over. The original `Point` is unchanged.

This is functional property modification — the same pattern found in Swift's value types and Kotlin's `copy()`. The compiler optimizes chained withers into single allocations, so the visual clarity of immutability doesn't come at a performance cost.

### Embedded Types and Property Elision

A type can **embed** another type as a property. A `Circle` constructor might pack a `center` of type `Point` alongside a `radius`. Nothing unusual so far — that's just composition.

What makes embedding powerful is **elision**: the embedded type's properties become accessible *as if they were properties of the outer type*. A `Circle` that embeds a `Point` as its center means `Circle.x` and `Circle.y` work directly — no need to first extract the `center` and then access its properties.


<figure style="text-align: center;">
    <img alt="Circle embedding Point with elided property access" width="25%" src="/blog/images/tutorial4.5.png">
    <figcaption>property elision — constructor for <code>Circle</code></figcaption>
</figure>

<figure style="text-align: center;">
    <img alt="Circle embedding Point with elided property access" width="40%" src="/blog/images/tutorial4.6.png">
    <figcaption>property elision — <code>Circle.x</code> accesses the embedded <code>Point</code>'s property directly</figcaption>
</figure>

The getter for `x` on a `Circle` reaches through the embedded `Point` automatically. The wither works the same way — setting `x` on a `Circle` creates a new `Circle` with a new embedded `Point`, all in one step. The nesting is invisible at the call site.

## Method Dispatch

When a graph contains a node labeled `distanceTo`, how does the compiler know which method to call? LQ uses **signature-based dispatch** — the types of all input terminals determine which method runs.

A method named `distanceTo` that takes `(Point, Point)` is a different method from `distanceTo` that takes `(Point, Line)`. The compiler examines the types flowing into the node's input terminals and selects the matching signature. No ambiguity. No guessing.

This is static dispatch — resolved at compile time. The compiler knows the types, matches the signature, and emits a direct call. Zero runtime overhead.

### Dynamic Dispatch with Any

When an input terminal is typed as `Any`, the compiler can't resolve the method at compile time. Instead, dispatch happens at runtime — the actual types of the values flowing through determine which method runs.

This is LQ's equivalent of virtual method dispatch. A method that accepts `(Any, Point)` will match against whatever concrete type arrives at runtime — `(Circle, Point)`, `(Rectangle, Point)`, `(Triangle, Point)` — selecting the appropriate implementation.

The mechanism is the same as static dispatch. The difference is *when* the matching happens: compile time for known types, runtime for `Any`. No separate vtable. No inheritance chain. Just signature matching applied at the moment the types are known.

### Dispatch on Embedded Types

Property elision has a counterpart in dispatch: a method that accepts `Point` also matches `Circle` — because `Circle` embeds `Point`. The compiler recognizes the embedded structure and routes the call accordingly.

This provides a form of **multiple inheritance** without inheritance. A type that embeds both `Point` and `Color` matches methods that accept `Point`, methods that accept `Color`, and methods that accept the full type. No diamond problem. No fragile base classes. The matching is structural — if the embedded type is present, the method applies.

Combined with `Any` for dynamic dispatch, embedded types give LQ the expressive power of class hierarchies — polymorphism, code reuse, structural subtyping — without the rigidity of an inheritance tree.

## "this"

Traditional object-oriented languages have a built-in concept of `this` (or `self`) — the object a method belongs to. The method is bound to the object. The dispatch is tied to the class hierarchy.

LQ has no built-in `this`. Instead, `this` is a **convention** — a pattern built on top of the mechanics already described.

Here's how it works: an input parameter is named `this`. Naming a parameter `this` creates a local called `this` inside the method. The default value of that parameter is also `this` — meaning if nothing is wired to the terminal, the method uses the current `this` from the calling context.
This creates familiar object-oriented behavior through composition of existing features:

**Instance methods.** A method with a `this` parameter typed as `Point` behaves like a method on the `Point` type. The `this` value arrives through the parameter, making the instance available inside the method. Properties are accessed by unpacking `this`.

**Chained calls.** When one method calls another without wiring anything to the callee's `this` terminal, the current `this` flows through automatically via the default value. Method calls on the same object chain naturally without explicit plumbing.

**Virtual dispatch.** Type the `this` parameter as `Any` and the method becomes dynamically dispatched — the actual type at runtime determines which implementation runs. This is the equivalent of a virtual method in a class-based language, achieved through the combination of `Any` typing and signature-based dispatch.

```python
class Point:
    def magnitude(self):
        return math.sqrt(self.x ** 2 + self.y ** 2)
```

In LQ, a `magnitude` method with a `this` parameter typed as `Point` unpacks `this` to get `x` and `y`, computes the result, and outputs it. No class declaration. No inheritance syntax. The method operates on `Point` because the types say so.

## No Classes, Same Power, And Then Some

The four concepts — spaces, constructors, dispatch, and `this` — combine to provide the same organizational power as class-based OOP without the rigidity:

**Spaces** group related methods without coupling them to a type hierarchy. Methods for `Point` can live alongside methods for `Line` in a `Geometry` space, or be spread across multiple spaces. The organization is for readability, not for the compiler.

**Constructors** are just methods with `pack` nodes. No special lifecycle. No inheritance chains to manage. Construction logic is ordinary dataflow.

**Dispatch** is based on what data actually flows, not on what class an object belongs to. Static when the types are known, dynamic when `Any` is involved. The same mechanism handles both.

**"this"** is a convention, not a keyword. Object-oriented patterns emerge from default parameter values and signature matching — not from language primitives.

This separation means LQ avoids the problems that plague class-based languages: fragile base class issues, deep inheritance chains, the expression problem. Types are defined by structure. Behavior is defined by signatures. Organization is defined by spaces. Each concern is independent.

## Next

With code organization and dispatch in place, upcoming posts will explore the type system in more depth, specifically some of the built in types such as array and map.
