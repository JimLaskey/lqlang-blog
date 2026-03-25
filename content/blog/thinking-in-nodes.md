+++
date = '2026-03-24T11:56:25-03:00'
draft = false
title = 'Thinking in Nodes'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "parallelism", "future-of-code", "programming-languages", "howto",  "LLM"]
+++

Previous posts introduced the idea behind LQ — a diagrammatic dataflow language where the program *is* the graph. This post gets hands-on. Starting with a small, complete example and then breaking it apart to explain the building blocks: nodes, terminals, connections, and data flow.

## The Example

What the example does: take a JSON string containing a list of people, parse it, iterate through the list, and extract each person's name and age.

In a text language, this might be a few lines of parsing and a for loop. In LQ, the entire program is a graph — and here it is:

<figure style="text-align: center;">
    <img alt="main method" width="40%" src="/blog/images/tutorial1.1.png">
    <figcaption><code>main</code> method</figcaption>
</figure>

<figure style="text-align: center;">
    <img alt="printPerson method" width="50%" src="/blog/images/tutorial1.6.png">
    <figcaption><code>printPerson</code> method</figcaption>
</figure>

That's the whole program. Now let's look at each piece.

## Text Literals

Data often enters an LQ graph through literal nodes. A text literal is the simplest kind — a node that holds a raw string value and provides it as output through a terminal at the bottom.

<figure style="text-align: center;">
    <img alt="text node with output terminal highlighted" width="40%" src="/blog/images/tutorial1.2.png">
    <figcaption>text node with output terminal highlighted</figcaption>
</figure>

The JSON string sits directly in the node. No variable declaration, no assignment statement, no `const json = '...'`. The data simply exists in the graph and flows outward through the terminal.

## Nodes and Terminals

Every operation in LQ is a **node** — typically a rounded rectangle with a label describing what it does. Nodes receive data through **input terminals** on the top (or left) edge and emit results through **output terminals** on the bottom (or right) edge.

<figure style="text-align: center;">
    <img alt="readJSON invocation with one input and one output" width="25%" src="/blog/images/tutorial1.3.png">
    <figcaption><code>readJSON</code> invocation with one input and one output</figcaption>
</figure>

`readJSON` (a library method) has one input terminal (a <code>String</code>) and one output terminal (an <code>Any</code> — because JSON can contain arrays, maps, numbers, strings, or booleans). The node transforms the raw JSON text into LQ's native data structures: Arrays and Maps.

## Connections

A **connection** is a wire between an output terminal and an input terminal. Data flows along the connection — from the text literal's output, into `readJSON`'s input.

<figure style="text-align: center;">
    <img alt="output of text node connected to input of readJSON node" width="40%" src="/blog/images/tutorial1.4.png">
    <figcaption>output of text node connected to input of <code>readJSON</code> node</figcaption>
</figure>

Connections are typed. The text literal emits a <code>String</code>. `readJSON` accepts a <code>String</code>. The types match, so the connection is valid. If someone tried to connect a number output to `readJSON`'s <code>String</code> input, the compiler via the IDE would highlight the connection immediately while editing — not as a runtime error, but as a structural error visible in the graph.

This is one of the advantages of a visual dataflow language. Type errors aren't buried in an error message referencing a line number. Type errors are flagged wire between two incompatible terminals — visible, obvious, and fixable by inspection.

## Iterating the Array

The JSON parses the text into an Array of Maps. To process each person, the graph uses a **forEach** node.

<figure style="text-align: center;">
    <img alt="the input to printPerson is annotated as forEach" width="40%" src="/blog/images/tutorial1.5.png">
    <figcaption>the input to <code>printPerson</code> is annotated as <code>forEach</code></figcaption>
</figure>

`forEach` receives the array and executes the node body once for each element. Inside the body, the current element — a single person Map — is available through a terminal. No index variable. No loop counter. No off-by-one errors. The iteration is structural: the node processes every element, and the body describes what happens to each one.

In a text language, this is a `for` loop or a `.forEach()` call. In LQ, the iteration is part of the graph's topology. The body is a subgraph — a graph within a graph — that receives one element at a time.

## Extracting Data from a Map

Each person is a Map — a set of key-value pairs. To pull out the name and age, the graph uses **get** nodes.

<figure style="text-align: center;">
    <img alt="printPerson method" width="50%" src="/blog/images/tutorial1.6.png">
    <figcaption><strong>printPerson</strong> method</figcaption>
</figure>


The input value for the <code>printPerson</code> method arrives via the triangular input parameter node at the top. `get` nodes take the Map as an input and uses the name of the node as the key. The output is the value associated with that key.

Notice that the two `get` nodes — one for "name" and one for "age" — sit side by side with no connection between them. Neither depends on the other. This means they execute in parallel. Not because of any special annotation or concurrency keyword, but because the graph *shows* that they're independent. Parallelism is the default. Sequential execution only happens when one node's output feeds into another node's input.

This is the core insight of dataflow: **data dependencies define execution order**. If there's no dependency, there's no ordering. The graph says exactly what depends on what, and nothing more.

Once the values are extracted they are fed to a special interpolation form of string literal to produce the result we want.

## Putting It Together

Zooming back out, the complete program reads top to bottom:

1. **Text literal** — the raw JSON string
2. **readJSON** — parses the string into an Array of Maps
3. **forEach** — iterates the array, processing each Map
4. **get "name"** and **get "age"** — extract values from each Map (in parallel)
5. **Interpolation string literal** - format the result
6. **println** — outputs the result

<figure style="text-align: center;">
    <img alt="main method" width="40%" src="/blog/images/tutorial1.1.png">
    <figcaption><code>main</code> method</figcaption>
</figure>

<figure style="text-align: center;">
    <img alt="printPerson method" width="50%" src="/blog/images/tutorial1.6.png">
    <figcaption><code>printPerson</code> method</figcaption>
</figure>

Result:

```text
The person Alice is 30 years old
The person Bob is 25 years old
The person Carol is 35 years old
```

Just nodes. No variables. No assignment. No semicolons. The data flows from top to bottom, transforming at each node. The shape of the program *is* the logic.

Compare this to the equivalent text code:

```javascript
const string = '[{"name":"Alice","age":30},{"name":"Bob","age":25},{"name":"Carol","age":35}]'
const people = JSON.parse(string);
for (const person of people) {
  console.log("The person", person.name, "is", person.age, "years old");
}
```

The text version is five lines — compact enough. But the data flow is implicit. The reader has to know that `JSON.parse` returns an array, that `person` is a map-like object, that `.name` and `.age` are independent accesses. All of that knowledge lives in the reader's head, not in the code.

In the LQ version, the data flow is explicit. The types are visible on the connections. The parallelism is visible in the topology. The iteration is visible in the structure. Nothing is implied — everything is shown.

## Next

This tutorial covered the fundamentals: literal nodes, operation nodes, terminals, connections, type flow, iteration, map access and interpolation. Upcoming posts will explore abutting, closures, control flow, composition, and how all of this looks when an AI agent builds the graph instead of a human.

The graph is the program. The shape is the logic. Start thinking in nodes.

## Afterword

For those who think the graphs use too much space, there are lots of shortcuts. For example, the printPerson method could be simplified to the following.

<figure style="text-align: center;">
    <img alt="literal abutting the println" width="60%" src="/blog/images/tutorial1.7.png">
    <figcaption>literal abutting the println</figcaption>
</figure>

Stay tuned.

