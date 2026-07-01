+++
date = '2026-05-12T10:00:00-03:00'
draft = false
title = 'Packing Spaghetti'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "future-of-code", "programming-languages", "howto", "LLM"]
+++

The most common objection to visual programming languages is the spaghetti problem. As programs grow, connections multiply, wires cross, and the diagram that was supposed to be clearer than text becomes a tangled mess. The criticism is fair — it's a real problem. Here's an example of AI-generated LQ code that demonstrates exactly this:

<figure style="text-align: center;">
    <img alt="AI generated code with tangled connections" width="60%" src="/blog/images/tutorial7.1.png">
    <figcaption>the spaghetti problem — connections everywhere</figcaption>
</figure>

LQ takes this problem seriously. The solution isn't to limit the language or hide complexity behind abstractions. The solution is **packing** — combining multiple values into structured types and letting the terminal system spread them back out where needed.

## The Problem

Consider a simple case: four literal values — `x`, `y`, `width`, `height` — passed to a `drawRectangle` method.

<figure style="text-align: center;">
    <img alt="four literals connected to drawRectangle" width="40%" src="/blog/images/tutorial7.2.png">
    <figcaption>four connections from four literals</figcaption>
</figure>

This is fine when everything is close together. But if the `drawRectangle` node is any distance from the literals, four separate wires stretch across the diagram. Multiply this across a real program and the spaghetti arrives fast.

For reference, the `drawRectangle` method itself is straightforward — an interpolated string fed to `println`:

<figure style="text-align: center;">
    <img alt="drawRectangle method with interpolated text block" width="50%" src="/blog/images/tutorial7.3.png">
    <figcaption><code>drawRectangle</code> — four inputs, one formatted output</figcaption>
</figure>

The method needs four values. The question is how to deliver them without four wires.

## Step 1: Pack and Unpack

The first tool is the **pack/unpack** pair introduced in [Spaces, Constructors, and Dispatch](/blog/spaces-constructors-dispatch). Pack combines multiple values into a single bundle. Unpack separates them back out.

<figure style="text-align: center;">
    <img alt="pack and unpack reducing four connections to one" width="50%" src="/blog/images/tutorial7.4.png">
    <figcaption>pack/unpack — four wires become one</figcaption>
</figure>

Four values pack into one connection. That single wire can travel any distance across the diagram. At the destination, unpack restores the original four values. The clutter is gone.

This works, but it's anonymous — the packed bundle has no type name, no semantic meaning. It's plumbing, not design.

## Step 2: A Named Type

Better: use a **constructor**. Pack the four values into a `Rectangle` and pass that single object to `drawRectangle`. The input terminal on `drawRectangle` is annotated as **unpack** — meaning the contents of the `Rectangle` are automatically spread to the method's parameters.

<figure style="text-align: center;">
    <img alt="Rectangle constructor with unpack terminal" width="50%" src="/blog/images/tutorial7.5.png">
    <figcaption><code>Rectangle</code> constructor + unpack terminal — one typed connection</figcaption>
</figure>

Now the single wire isn't just a bundle of values — it carries a `Rectangle`. The type has meaning. The unpack terminal tells the method to extract the properties and distribute them to the matching parameters. One connection, full type safety, zero clutter.

## Step 3: Composing Types

The four values don't have to be flat. A rectangle is naturally two things: a position and a size. The `x` and `y` values feed a `Point` constructor. The `width` and `height` feed a `Size` constructor. Both results connect to `drawRectangle` through unpack terminals.

<figure style="text-align: center;">
    <img alt="Point and Size constructors with unpack terminals" width="50%" src="/blog/images/tutorial7.6.png">
    <figcaption>composed types — <code>Point</code> and <code>Size</code> unpacked into the method</figcaption>
</figure>

Two wires instead of four. Each wire carries a meaningful type. The unpack terminals spread `Point` into `(x, y)` and `Size` into `(width, height)`. The method's parameter names match the property names — no mapping needed.

## Step 4: Full Composition

Take it one step further. The `Point` and `Size` feed a `Rectangle` constructor through unpack terminals. The single `Rectangle` then connects to `drawRectangle`.

<figure style="text-align: center;">
    <img alt="Point and Size into Rectangle constructor, then to drawRectangle" width="50%" src="/blog/images/tutorial7.7.png">
    <figcaption>full composition — types build into types, one wire at the end</figcaption>
</figure>

Four primitives → two intermediate types → one composed type → one connection. The diagram is clean. The types are meaningful. The structure matches how the data is actually organized.

## Unpacking in Iteration

The unpack terminal combines naturally with forEach. Consider building a menu from an array of `MenuRow` objects. Each `MenuRow` contains the properties needed by the `windowMenuAddItem` method.

<figure style="text-align: center;">
    <img alt="table of MenuRow items with unpacking forEach terminal" width="60%" src="/blog/images/tutorial7.8.png">
    <figcaption>forEach + unpack — iterating and spreading in one terminal</figcaption>
</figure>

A table node produces an array of `MenuRow` items. The array connects to `windowMenuAddItem` through a **forEach unpack** terminal — a forEach terminal annotated with unpack. For each `MenuRow` in the array, the properties are spread to the method's parameters. Iteration and unpacking happen at the same connection point.

This is the terminal annotation system from [Iteration Through Terminal Annotation](/blog/iteration-through-terminal-annotation) combined with the structural typing from this post. No loop. No manual property extraction. One annotated terminal does both jobs.

## The Spaghetti Solution

The spaghetti problem is real, but it's a symptom, not a disease. The disease is **unstructured data flowing through too many connections**. The cure is structure: pack related values into types, and let unpack terminals spread them where needed.

The progression tells the story:

1. **Raw connections** — four wires, maximum clutter
2. **Pack/unpack** — one wire, anonymous bundle
3. **Named type** — one wire, typed and meaningful
4. **Composed types** — types built from types, one wire at the end
5. **Iteration + unpack** — the pattern scales to collections

Each step reduces visual complexity while increasing semantic clarity. The diagram gets simpler *and* more informative — not one at the expense of the other.

Visual programming doesn't have to mean spaghetti. It means thinking about structure.
