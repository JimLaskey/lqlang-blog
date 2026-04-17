+++
date = '2026-04-14T10:00:00-03:00'
draft = false
title = 'Control Flow from Closures'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "control-flow", "closures", "future-of-code", "programming-languages", "howto", "LLM"]
+++

[The previous post](/blog/abutting-and-closures) ended with a claim: control flow in LQ is just methods that accept closures. No special syntax. No reserved keywords. Conditionals, loops, error handling — all built from the same pieces as any other operation.

This post makes good on that claim. Walking through four fundamental control structures — `if`, `guard`, `case` and `try/catch` — each one a regular method, each one a small recombination of closures and abutting.

## Abutting closures to a node

When a closure is abutted to a node, the closure becomes the last input argument of the node. This gives the node control over when — or whether — the closure is invoked. If a stack of closures are abutting the node then an array of closures is passed to the node. The node can select which and when the closures are actually invoked. This provides a powerful mechanism for controlling flow in LQ. Using this basic mechanism, developers can create their own control methods.

## if / else

The simplest control structure. `if` is a method that takes a boolean as input and one or two abutted closures.

<figure style="text-align: center;">
    <img alt="if method with one abutted closure (then branch only)" width="40%" src="/blog/images/tutorial3.1.png">
    <figcaption><code>if</code> with a single closure — the then branch</figcaption>
</figure>

When the boolean is `true`, the closure executes. When `false`, the closure is skipped. With one closure, that's the whole story.

For an else branch, abut a second closure below the first. The first closure is the then-branch. The second is the else-branch.

<figure style="text-align: center;">
    <img alt="if method with two stacked closures (then and else branches)" width="40%" src="/blog/images/tutorial3.2.png">
    <figcaption><code>if</code> with two stacked closures — then and else</figcaption>
</figure>

Notice what isn't here: no `else` keyword. No braces. No nested syntax. The two branches are just two closures stacked beneath the `if` node, each containing the subgraph for that path.

In a text language:

```python
if true:
    println("Success");
else:
    println("Failure");
```

In LQ: an `if` node with the boolean input, the first closure containing `println("Success")`, the second closure containing `println("Failure")`. Same logic, slightly different representation.

The compiler dissolves the closures at compile time. The result is native conditional branch instructions — the same machine code a C compiler would emit for the equivalent `if/else`. No closure objects. No indirection.

### Inputs and Outputs

The closures inside an `if` abutment aren't isolated — their inputs and outputs *are* the inputs and outputs of the entire abutment. Data flows into the `if`, through whichever branch executes, and out the other side.

<figure style="text-align: center;">
    <img alt="if abutment with closures outputting Success and Failure strings to println" width="40%" src="/blog/images/tutorial3.2a.png">
    <figcaption>closure outputs become the abutment's output — fed to <code>println</code></figcaption>
</figure>

In this example, the then-closure outputs the string "Success" and the else-closure outputs "Failure". The selected string flows out of the `if` abutment as a whole, arriving at the `println` node below. The abutment behaves as a single node with one output — which branch produced the value is an internal detail.

This is the dataflow equivalent of a ternary expression. The `if` abutment is an expression, not just a statement. Data goes in, data comes out.

### Iteration over an Abutment

The `if` abutment is also treated as a single node when it comes to iteration terminals. Attach a `forEach` input terminal and the entire abutment — condition, closures, and all — executes once for each element.

<figure style="text-align: center;">
    <img alt="array of booleans with forEach terminal iterating the entire if abutment" width="40%" src="/blog/images/tutorial3.2b.png">
    <figcaption>an array of booleans iterated against the entire <code>if</code> abutment</figcaption>
</figure>

Pass an array of booleans to the `if` abutment with a `forEach` input terminal, and the abutment runs once per boolean — selecting the then or else branch for each element. The abutment is a single composable unit. Iteration, branching, and output all work together because the abutment is just a node like any other.

## guard

In LQ, `guard` is a method that takes an array guarded closures. If the guard(s) of the first closure fails then control passes on to the next closure and so on.

<figure style="text-align: center;">
    <img alt="guard method with abutted closures" width="40%" src="/blog/images/tutorial3.3.png">
    <figcaption><code>guard</code> — closure runs on success otherwise goes to the next</figcaption>
</figure>

In the example, the input to the guard abutment is checked in each closure against a literal using is equal. Since the "a" test fails it goes to the next closure. This time the "b" test succeeds and the closure prints "case 2". The last closure is typically the catchall if all guards fail.

## case

`case` is where stacked closures become true alternatives.

The `case` node takes an integer input and an arbitrary number of stacked closures. The integer selects which closure runs — index 0 picks the first, index 1 picks the second, and so on. The last closure in the stack is the **default**, executed when the index doesn't match any earlier closure.

<figure style="text-align: center;">
    <img alt="case method with multiple stacked closures, integer index input" width="40%" src="/blog/images/tutorial3.4.png">
    <figcaption><code>case</code> — integer selects which stacked closure runs</figcaption>
</figure>

In a text language, this is a `switch` statement:

```javascript
switch (index) {
  case 0: println("case 0"); break;
  case 1: println("case 1"); break;
  default: println("case 2"); break;
}
```

In LQ, the three behaviors are three stacked closures abutting a `case` node. The `index` input flows into the index terminal. The compiler generates a jump table — the same optimization a C compiler applies to a dense `switch`.

The stacking pattern from the previous post — where stacked literals form an array — extends naturally here. Stacked closures form alternatives. Same spatial mechanic. Same principle: structure expressed through arrangement, not punctuation.

For sparse or non-integer dispatch (matching strings, types, or patterns), the language provides higher-level matching methods built on top of `case`. But the primitive is just an integer index into a stack of closures.

## try / catch

Error handling follows the same pattern. `try/catch` is a single node with two stacked closures: the try block on top, the catch block below.

<figure style="text-align: center;">
    <img alt="try/catch node with two stacked closures - try block and catch handler" width="65%" src="/blog/images/tutorial3.5.png">
    <figcaption><code>try/catch</code> — protected block on top, handler below</figcaption>
</figure>

Execution enters the try closure. If the subgraph completes normally, the result flows out of the `try/catch` node and execution continues. If an error is thrown anywhere inside the try closure, execution jumps to the catch closure, which receives the error as input.

```python
try:
    result = risky_operation()
    process(result)
except Exception as e:
    log_error(e)
```

In LQ, the `try/catch` node has the protected operations in its first closure and the error handler in its second. The error type appears as an input terminal on the catch closure. The graph makes the recovery path immediately visible — no scrolling to find a distant `except` block, no wondering which `try` a particular `catch` belongs to. The pairing is spatial.

For methods that can throw, the type system tracks which errors propagate. A `try/catch` that doesn't handle a thrown error type is flagged at compile time — the structural error appears in the IDE while editing, not at runtime.


## The Pattern Repeats

Four control structures. One mechanism.

Every one of them is a method that accepts closures. `if` and `guard` use abutting to attach branches. `case` and `try/catch` use stacking to express alternatives. The visual operators are abutment and stacking. The semantic primitive is the closure.

This is what "closures all the way down" means in practice. New control flow patterns don't require language extensions — they require library methods that accept closures. A `retry` that re-executes a closure on failure. A `timeout` that races a closure against a deadline. A `transaction` that commits or rolls back. A `parallel` that runs stacked closures concurrently. None of these need new syntax. All of them follow the same pattern as the four built-in structures.

The compiler erases the visual structure. Closures dissolve into native control flow. The runtime cost is the same as hand-written C or Rust. The graph is the design tool. The compiled output is the execution.

## Next

With control flow established, upcoming posts will explore the type system — how LQ tracks types through the graph, how generic methods work, and how the compiler verifies entire programs structurally. After that: the agent serialization format that lets AI write LQ programs directly, without going through the visual representation at all.

The pieces are in place. The system is starting to show its shape.
