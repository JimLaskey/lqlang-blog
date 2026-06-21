+++
date = '2026-05-05T10:00:00-03:00'
draft = false
title = 'Iteration Through Terminal Annotation'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "future-of-code", "programming-languages", "howto", "LLM", "parallelism", "iteration"]
+++

Earlier posts introduced `forEach` as a way to iterate an array — attach a forEach terminal and the node executes once per element. That was the simplified version. The full story is richer: LQ has **seven terminal types**, and the choice of terminal controls how data enters, exits, and flows through iteration. Iteration, gathering, reduction, and parallelism are all governed at the terminal — not in the node logic.

This post covers the terminal types, what each one does, and how they combine to express the full range of collection processing.

## Terminal Types

A normal terminal — the small circle seen in every diagram so far — passes a single value in or out. That's the default. But terminals can take other shapes, each one changing how data flows during iteration.

LQ defines seven terminal types:

- **Normal** — single value, no iteration
- **ForEach** — iterate forward
- **ForEach Reverse** — iterate backward
- **ForEach FlatMap** — iterate forward, recursing into nested collections
- **ForEach FlatMap Reverse** — iterate backward, recursing into nested collections
- **Reduce** — fold with an accumulator
- **Parallel** — iterate with each iteration on a separate thread

Each terminal type behaves differently depending on where it appears: as an input or output on a node, or as an input parameter or output parameter on a method. The terminal shape is the visual cue — the shape tells the reader (and the compiler) what kind of iteration is happening at that connection point.

## ForEach

The forEach terminal drives iteration. On the **input** side, each iteration calls the `next` method on the input type to get the next element. The iteration continues until the collection is exhausted.

<figure style="text-align: center;">
    <img alt="forEach input terminal iterating an array" width="40%" src="/blog/images/tutorial6.1.png">
    <figcaption>forEach input — iterating element by element</figcaption>
</figure>

On the **output** side, forEach does the inverse: **gathering**. Each iteration's output is collected using a builder — an array builder for arrays, a string builder for strings, with support for user-defined types in the future. The builder's `append` method accumulates results across iterations, and the final collection flows out when iteration completes.

<figure style="text-align: center;">
    <img alt="forEach output terminal gathering results into an array" width="40%" src="/blog/images/tutorial6.2.png">
    <figcaption>forEach output — gathering iteration results into a collection</figcaption>
</figure>

This is the key insight: **input forEach iterates, output forEach gathers**. A node with a forEach input terminal and a forEach output terminal takes a collection apart element by element, transforms each element, and reassembles the results into a new collection. That's `map` — not as a special method, but as a natural consequence of terminal types.

### ForEach on Parameters

forEach terminals also appear on method parameters, where the behavior is slightly different:

On an **input parameter**, forEach gathers — collecting multiple values passed to that parameter position into an array. This is LQ's equivalent of variadic arguments.

On an **output parameter**, forEach spreads — distributing elements from an array output across multiple outgoing connections.

### ForEach Reverse

ForEach Reverse works identically to forEach but iterates in reverse order, using `prev` instead of `next`. The gathering side builds the collection in the corresponding reverse order.

### ForEach FlatMap

ForEach FlatMap extends forEach by recursing into nested collections. Where a regular forEach iterates the top-level elements of an array, flatMap descends into nested arrays and processes each leaf element individually. The result is a single flat collection regardless of the nesting depth of the input.

ForEach FlatMap Reverse does the same in reverse order.

## Reduction

Reduction is the classic fold: take a collection and reduce it to a single value using an accumulator.

In LQ, reduction uses a dedicated terminal type — the **reduce** terminal. Reduce terminals come in **pairs**: one input terminal and one output terminal on the same node. The pairing is the key to understanding how reduction works.

<figure style="text-align: center;">
    <img alt="reduce terminals showing accumulator pattern" width="50%" src="/blog/images/tutorial6.3.png">
    <figcaption>reduce — paired input/output terminals carry the accumulator</figcaption>
</figure>

On the first iteration, the reduce **input** takes the initial value — the seed of the accumulation. On each subsequent iteration, the reduce input takes the value from the matching reduce **output** of the previous iteration. The reduce output on the final iteration produces the result.

In a text language, this is:

```python
result = 0                    # initial value → reduce input
for item in items:
    result = result + item    # previous output → next input
# result is the final output  # reduce output
```

In LQ, the same pattern is expressed structurally. The reduce input terminal carries the accumulator into each iteration. The node transforms it. The reduce output terminal carries the updated accumulator to the next iteration. On the final iteration, the reduce output becomes the node's result.

Reduce terminals cannot appear on method parameters — they only make sense within an iteration context.

## Filtering

Iteration often needs to skip elements that don't meet a condition. In text languages, this is `filter` — a separate method that produces a new collection. In LQ, filtering happens *inside* the iteration using the **continue** method.

`continue` takes a boolean argument. If the value is `true`, the current iteration is skipped — no output is produced, no accumulator is updated, and execution moves to the next element. If `false`, the iteration proceeds normally.

<figure style="text-align: center;">
    <img alt="continue method filtering elements during iteration" width="50%" src="/blog/images/tutorial6.3a.png">
    <figcaption><code>continue</code> — skip the iteration when the condition is true</figcaption>
</figure>

This means `filter` isn't a separate operation. Filtering is part of the iteration itself. A node with a forEach input, a `continue` check, and a forEach output implements filter-then-map in a single pass — no intermediate collection, no extra allocation.

Combined with reduction, `continue` expresses "sum only the positive values" or "count only the matches" without needing specialized methods. The iteration, the filtering, and the accumulation all happen in the same node.

### Break

The counterpart to `continue` is **break**. Where `continue` skips a single iteration, `break` exits the iteration entirely. Like `continue`, `break` takes a boolean argument — if `true`, iteration stops immediately and the current accumulated output is the final result.

Together, `continue` and `break` give full control over iteration flow. `continue` is the filter. `break` is the early exit. Both are methods inside the iteration body, not terminal annotations — keeping the terminal vocabulary small while the control options stay expressive.

## Parallel

The parallel terminal type works like forEach — iterating, gathering, spreading — except each iteration runs on a **separate thread**.

Same input behavior: the `next` method provides elements to each thread. Same output behavior: results are gathered into a collection. The difference is execution — iterations run concurrently rather than sequentially.

This is the parallelism story that earlier posts described at the paradigm level, now made concrete at the terminal level. Switching a forEach terminal to a parallel terminal changes sequential iteration to concurrent iteration. The node logic doesn't change. The connections don't change. The terminal shape is the only difference.

Like reduce, parallel terminals cannot appear on method parameters.

## Combining Terminal Types

The real power emerges when terminal types combine on the same node.

A node with a **forEach input** and a **normal output** processes each element and keeps only the last result — useful for finding a final value in a sequence.

A node with a **forEach input** and a **forEach output** transforms a collection element by element — the `map` pattern.

A node with a **forEach input** and a **reduce output** folds a collection into a single value — the `reduce` pattern.

A node with a **parallel input** and a **forEach output** processes elements concurrently and gathers the results into a collection — parallel map.

A node with a **forEach input**, a **reduce pair**, and a **forEach output** can simultaneously iterate, accumulate, and gather — expressing complex collection pipelines in a single node's terminal configuration.

The terminal types are orthogonal. Any combination that makes semantic sense is valid. The compiler verifies that the types flowing through each terminal are compatible, and the IDE flags mismatches while editing.

## Why Terminals, Not Methods

In most languages, `map`, `filter`, `reduce`, `flatMap`, and `parallelMap` are separate methods — each one a different function call with its own name and signature. In LQ, all of these are the **same node** with **different terminal types**. The behavior lives at the connection point, not in the operation.

This means new iteration patterns don't require new methods. Changing a forEach terminal to parallel converts sequential processing to concurrent processing. Changing a normal output to a forEach output converts a scalar operation to a collection-building one. The vocabulary is small — seven terminal types — but the expressiveness is combinatorial.
