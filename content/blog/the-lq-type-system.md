+++
date = '2026-04-28T10:00:00-03:00'
draft = false
title = 'The LQ Type System'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "future-of-code", "programming-languages", "howto", "LLM", "types"]
+++

> *> Apologies for the gap between posts. I've been away, and the time has gone to heavy lifting — porting Skia to LQ and building an LQ UI library. All good work, and I'm excited about where things are headed.*

LQ is statically typed with **value semantics** and **immutability by default**. There are no classes and no inheritance — every type is a constructor. "Methods all the way down." A value is built by calling its type name, and "mutation" means producing a new value, never writing in place. An `Any` type provides a typed escape hatch when dynamic dispatch is needed.

This post walks through the core types, what makes each one interesting, and shows real LQ programs that exercise them.

> The geometry and graphics types — `Point`, `Size`, `Rect`, `Color`, `Paint`, `View`, and others — are a separate layer built on top of these primitives.

## Integers

Four widths, signed and unsigned: `i8`/`u8`, `i16`/`u16`, `i32`/`u32`, `i64`/`u64`. The default integer is `i64`.

One design choice worth calling out: **signedness is operator-driven, not type-driven**. Operators assume signed semantics by default. Prefix with `#` to get unsigned behavior for the operations where it actually matters — comparisons (`#<`, `#<=`, `#>`, `#>=`), division and modulo (`#/`, `#%`), and right shift (`#>>`). The common signed case stays clean. Exact control is there when needed.

The difference matters. `-1` in binary is all ones — under signed comparison it's negative, under unsigned comparison it's the largest possible value:

<figure style="text-align: center;">
    <img alt="signed vs unsigned comparison" width="35%" src="/blog/images/tutorial5.1.png">
    <figcaption>same value, different answer — <code>&lt; 0</code> vs <code>#&lt; 0</code></figcaption>
</figure>

```text
Signed true and unsigned false
```

Only comparison, division/modulo, and right-shift have `#` forms — the operations where signedness actually changes the result.

## Floats

IEEE-754: `f32` (single) and `f64` (double). The default is `f64`. All geometry and coordinate types in the UI layer use `f64` as well.

## Boolean

`boolean`, with values `true` and `false`.

## Array

Array syntax is `[ElementType]` — `[i64]`, `[String]`, `[[i32]]` for nested arrays.

The elegant part: **arrays and cursors are unified**. A cursor is simply an array that spans a sub-range of the same backing buffer. Slicing doesn't copy — it just narrows the window.

This makes cursor operations cheap and composable:

- `next` / `prev` — first or last element plus the cursor advanced or retreated by one
- `find(x)` — cursor positioned at the first match
- `before` / `after` — elements before or after the cursor in the original array
- `withBefore` / `withAfter` — those elements *plus* the cursor
- `empty` — true when the range is exhausted

The operations that reach outside the cursor (`before`, `after`, `withBefore`, `withAfter`) take the original array as an explicit argument. And because everything is immutable, array "edits" produce new arrays through construction and cursor recombination rather than mutation.

<figure style="text-align: center;">
    <img alt="array find then withBefore" width="40%" src="/blog/images/tutorial5.2.png">
    <figcaption>cursor slicing — <code>find</code> then <code>withBefore</code> over an array</figcaption>
</figure>

```text
[apple, blueberry]
```

`find` locates `"blueberry"`, `withBefore` returns the cursor plus everything before it — a fresh view over the same backing buffer, never a copy.

## String

Strings are UTF-8 encoded and immutable. Because of the internal representation, strings share the cursor and slice operations with arrays — slicing a string is pointer arithmetic, not copying. Since the encoding is UTF-8, cursor operations like `next` and `prev` may advance multiple bytes and return a codepoint integer value.

Typical operations include `join`, `split`, `replace`, `upper`, `lower`, `trim`, `find`, `contains`, `length`, and `substring`. String interpolation literals (`"Result: \{x}"`) format values directly.

<figure style="text-align: center;">
    <img alt="string find then withBefore" width="35%" src="/blog/images/tutorial5.3.png">
    <figcaption>string slicing — same cursor algebra as arrays</figcaption>
</figure>

```text
apple blueberry
```

The same `find` and `withBefore` pattern as arrays, applied to a string. No copying — just new windows over the same buffer.

## Map

Maps are hash-based with copy-on-write semantics. A missing key returns the default value for the type — no null, no exception.

Maps are constructed with `map(keysArray, valuesArray)` or, in the IDE, via a **table node** set to `Map`, `StringMap`, or `Enum`. (`StringMap` keys are unquoted identifiers; `Enum` is a `Map<String, i64>` of named constants.)

<figure style="text-align: center;">
    <img alt="map construction, get, and put" width="40%" src="/blog/images/tutorial5.4.png">
    <figcaption>building and reading a <code>Map</code> — <code>get</code> and <code>put</code></figcaption>
</figure>

```text
2.99
[cherry: 2.99, date: 10, blueberry: 3.7, apple: 1.25]
```

`get` reads a value. `put` produces a *new* map with the added entry, leaving the original untouched. Immutability by default — the pattern that runs through every LQ type.

## Range and StepRange

A `Range` is a half-open interval. Two literal forms:

- `1..10` is **exclusive** — the set 1 through 9
- `1...10` is **inclusive** — the set 1 through 10

A `Range` displays in its inclusive form, so `1..10` prints as `1...9`.

<figure style="text-align: center;">
    <img alt="exclusive vs inclusive range" width="40%" src="/blog/images/tutorial5.5.png">
    <figcaption>exclusive vs inclusive — two literal forms, one element apart</figcaption>
</figure>

```text
Exclusive 1...9 and inclusive 1...10
```

`StepRange` adds a stride — supporting positive and negative steps. A step of zero yields an empty range.

## Closure

A closure is a first-class value — behavior packaged as data, as covered in [Abutting and Closures](/blog/abutting-and-closures). Closures and callbacks share the same representation, so a block of dataflow can be passed wherever a function is expected.

<figure style="text-align: center;">
    <img alt="closure invoked with an argument array" width="30%" src="/blog/images/tutorial5.6.png">
    <figcaption>a closure value — <code>invoke</code> spreads inputs and gathers the result</figcaption>
</figure>

```text
[30]
```

The framed subgraph *is* the closure — here `(a, b) → a + b`. `invoke` spreads the array `[10, 20]` into the parameters and collects the output.

## Any

`Any` is the one type that carries runtime type information. Boxing wraps a concrete value into `Any`; unboxing extracts it. `typeOf` returns the type as a value.

Type identity is efficient — each type has a unique layout descriptor, so `typeOf(x) == "Color"` compiles down to a pointer comparison, not a string match.

<figure style="text-align: center;">
    <img alt="Color value and its typeOf" width="40%" src="/blog/images/tutorial5.7.png">
    <figcaption><code>Any</code> and <code>typeOf</code> — runtime type identity</figcaption>
</figure>

```text
The structure Color(r: 255, g: 255, b: 127, a: 255) is of type Color
```

`Any` is what makes heterogeneous collections, generic runtime dispatch, and reflective checks possible — without giving up static typing everywhere else.

## Void

`Void` is the no-value type — the result of `println`, side-effecting calls, and similar operations. Never required to be written explicitly.

## Type Coercion

When two different types meet as operands of a binary operator, LQ promotes both to a common type — mixing `i32` and `i64` resolves to `i64`, mixing `i64` and `f64` resolves to `f64` — rather than raising an error. `Any` participates when one side is dynamic.

## Just Values

Every type in LQ follows the same principles: constructed by calling its name, immutable by default, modified by producing new values. Arrays, strings, maps, ranges, closures — each one is a value that flows through the graph. No reference semantics. No hidden mutation.

## Next

With core types covered, upcoming posts will cover how these types fold into the language; forEach, reduction and gathering.
