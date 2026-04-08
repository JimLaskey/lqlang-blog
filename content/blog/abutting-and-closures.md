+++
date = '2026-04-07T10:21:11-03:00'
draft = false
title = 'Abutting and Closures'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "parallelism", "future-of-code", "programming-languages", "howto",  "LLM"]
+++

The [previous tutorial](/blog/thinking-in-nodes) ended with a teaser — a compressed version of the `printPerson` method where a string literal physically touched a `println` node, merging into a single compact structure. That wasn't just a layout trick. That was **abutting** — one of the features that makes LQ a shade different from other visual languages.

This post explains abutting from the ground up, then introduces the deeper concept that makes it powerful: closures.

## Abutting: The Simple Case

In most visual languages, nodes are isolated boxes connected by wires. Every operation floats independently, and the only relationship between nodes is the explicit connection.

In LQ, nodes can **abut** — placed adjacent to each other, sharing a boundary, merging their behavior. The simplest case: a literal abutting a `println`.

<figure style="text-align: center;">
    <img alt="detached literal vs abutted literal with println" width="40%" src="/blog/images/tutorial2.1.png">
    <figcaption>detached vs abutted — same semantics, different layout</figcaption>
</figure>

In the detached form, the literal has an output terminal, a wire carries the value, and `println` has an input terminal. Three visual elements for one simple idea: print this string.

In the abutted form, the literal sits directly against `println`. No wire. No terminals between them. The literal becomes **the** input argument. The spatial relationship *is* the connection.

This is the form shown in the afterword of the previous post — a string literal abutting `println` to produce a single compact expression.

## Stacking

Abutting gets more interesting when literals **stack**.

Place two string literals on top of each other, abutting the same node, and something changes: the values form an **array**. Stacking is how LQ represents multiple values of the same type flowing into a single input.

<figure style="text-align: center;">
    <img alt="three string literals stacked, abutting println, forming an array" width="25%" src="/blog/images/tutorial2.2.png">
    <figcaption>stacked literals form an <code>Array&lt;String&gt;</code></figcaption>
</figure>

No array syntax. No square brackets. No commas. The visual arrangement *is* the data structure. Three literals stacked produce an array of three elements.

This is a small example of a larger principle: in LQ, structure can be expressed through spatial arrangement, not punctuation. The stacking pattern will reappear later in a more powerful form — stacked closures representing alternatives — but the mechanic is the same.

## Closures

To go beyond literals, LQ needs a way to represent *behavior* — not a fixed value, but a computation that can be passed around and executed later. That's a **closure**.

In text languages, a closure is a function that captures variables from its surrounding scope:

```python
greet = lambda name: f"Hello, {name}"
```

In LQ, a closure is a **node that contains a subgraph** — a small dataflow program inside a node. The closure has input terminals (its parameters), an internal graph (its logic), and output terminals (its results).

<figure style="text-align: center;">
    <img alt="closure node with internal subgraph, connected by wire" width="35%" src="/blog/images/tutorial2.3.png">
    <figcaption>a closure node — behavior packaged as data</figcaption>
</figure>

The critical thing: a closure describes behavior *to be executed later*. The closure node itself doesn't run the subgraph. The closure packages the behavior and passes it along the wire as data — just like a literal passes a value.

### Capturing Values

What makes a closure more than just a subgraph is **capture** — connections from the surrounding graph that feed into the closure's interior. A closure doesn't just receive its explicit parameters. A closure can also pull in values from the enclosing scope through connections that cross the closure boundary.

```python
threshold = 30
is_senior = lambda person: person["age"] > threshold
```

In that Python example, `threshold` is captured — it lives outside the lambda but is used inside it. In LQ, this is visible in the graph: a wire from a node outside the closure crosses into the closure's interior. The captured value is explicit, not implicit. There's no wondering which variables a closure "closes over" — the connections show it.

<figure style="text-align: center;">
    <img alt="closure with parameter input and captured value from outside" width="50%" src="/blog/images/tutorial2.4.png">
    <figcaption>parameter vs captured value — capture is visible in the graph</figcaption>
</figure>

### Closures as Continuations

Closures that capture context and defer execution also open the door to **continuations** — the ability to suspend and resume computation at a specific point. This is a deeper topic that deserves its own post, but worth noting: the same closure mechanics that support sorting comparators and loop bodies are the foundation for more advanced control flow patterns like coroutines and structured concurrency. More on this in a future post.

Closures in LQ are compile-time constructs. The compiler sees the closure, understands the captured context and the internal graph, and dissolves the entire structure into native control flow. No heap allocation. No runtime indirection. The visual representation is explicit, but the compiled output is as efficient as hand-written C.

## Invoke

A closure sitting in the graph is inert — behavior waiting to happen. To actually execute it, LQ uses **invoke**.

`invoke` is a general-purpose node that takes a closure as input, executes the closure's subgraph, and produces the result. Think of it as the "call" operation for closures. The `invoke` inputs are the closure and an array of closure inputs. The `invoke` output is an array of the closure outputs.

<figure style="text-align: center;">
    <img alt="closure connected by wire to invoke node" width="50%" src="/blog/images/tutorial2.5.png">
    <figcaption><code>invoke</code> executes the closure's subgraph</figcaption>
</figure>

In a text language, this is the difference between defining a function and calling it:

```python
greet = lambda name: f"Hello, {name}"   # define
result = greet("Alice")                   # invoke
```

In LQ, the closure node is the definition. The `invoke` node is the call. The wire between them carries behavior as data.

This separation matters because closures can be passed *anywhere* before being invoked. A closure can travel through multiple nodes, be stored, be selected from alternatives, or be passed to a method that decides when and how to execute it.

## Abutting with Closures: The Payoff

Now the two concepts come together.

Consider sorting. Every developer has written a comparator — a small piece of behavior that takes two elements and decides which comes first:

```python
people.sort(key=lambda person: person["age"])
```

That `lambda` is a closure passed to `sort`. In LQ, the comparator is a closure node containing the comparison logic. Connected by a wire, the closure floats separately from the `sort` node:

<figure style="text-align: center;">
    <img alt="closure connected to sort node by wire — detached form" width="50%" src="/blog/images/tutorial2.6.png">
    <figcaption>detached — closure linked to <code>sort</code> by wire</figcaption>
</figure>

Functional, but the relationship between the comparator and the sort is only visible by tracing the wire. Now abut the closure to the sort node:

<figure style="text-align: center;">
    <img alt="closure abutting sort node — shared boundary" width="35%" src="/blog/images/tutorial2.7.png">
    <figcaption>abutted — "sort <em>using this</em>"</figcaption>
</figure>

The abutted version reads like a sentence: "sort *using this*." The comparator belongs to the sort — visually, spatially, semantically. No wire to trace. No reference to follow. The behavior is right there.

## Control Flow from Closures

Here's where the design comes full circle: **control flow in LQ is just methods that accept closures**.

The `forEach` from the [previous tutorial](/blog/thinking-in-nodes) was already a closure in disguise. `forEach` is a regular method that takes an array and a closure. The closure is the loop body. There's no special "loop node" built into the language.

The same applies to conditionals. An `if` is a node that takes a boolean and one or two closures — the "then" branch and the optional "else" branch. The closures abut the `if` node, making the branches visually adjacent to the condition.

No special control flow syntax. Just nodes and closures. And remember the stacking pattern from earlier — stacked closures will become the visual equivalent of pattern matching. But that's a topic for a future post.

## Why This Matters

**Readability.** Abutting keeps related logic together. The comparator lives next to the sort. The loop body lives next to the iteration.

**Composability.** Because control flow is just method calls with closures, new control flow patterns are ordinary library methods. A `retry` that re-executes a closure on failure. A `timeout` that races a closure against a deadline. No language extension required.

**Zero overhead.** Closures dissolve at compile time. The visual structure is for the developer or agent. The compiled output is flat, efficient native code through LLVM.

## Next

Upcoming posts will explore stacked closures as alternatives, terminal kinds, the type system, and how an AI agent constructs these structures programmatically. The building blocks are in place. The structures get more interesting from here.
