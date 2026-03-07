+++
date = '2026-03-03T10:16:58-04:00'
draft = false
title = 'Mr. Spock Does Not Code in ASCII'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "parallelism", "future-of-code", "programming-languages", "manifesto",  "LLM"]
+++

*Why the future of programming looks nothing like a text file*

---

It's 2026, and we're still typing `if (x > 0) { return true; }` into monospaced text editors like it's a teletype terminal in 1957. We've reinvented every other human-computer interface — touch, voice, gesture, spatial computing — and yet the act of *programming* remains stubbornly anchored to sequential lines of ASCII characters. Rows of glyphs. Typed one keystroke at a time. Into a rectangle.

Why?

Not because it's optimal. Because it's *familiar*. And familiarity, in computing, is the most dangerous form of inertia.

## The Teletype Hangover

Textual programming languages descend directly from the constraints of 1940s and 1950s hardware. Punched cards. Line printers. Fixed-width character sets. When Grace Hopper et al designed the first compilers, the *only* output device was a printer. The only input device was a card reader or paper tape. Of course the language looked like text — what else could it look like?

But here's the thing: those constraints evaporated decades ago. We have retina displays capable of rendering millions of pixels in real time. Multi-touch surfaces. Stylus input with sub-millimeter precision. GPUs that can composite thousands of graphical objects at 120 frames per second. And yet, the dominant model for expressing computation is still a sequence of characters arranged left-to-right, top-to-bottom, in a monospaced font.

That's not tradition. That's a failure of imagination.

## The Keyboard Is Leaving the Room

Look around. The dominant computing devices on the planet — phones and tablets — don't have hardware keyboards. Over three billion people carry a smartphone. Most of them have never owned a device with a physical keyboard attached. The keyboard was once the assumed gateway to computing power. Now it's an optional accessory.

And yet, if any of those three billion people want to *program* — to express logic, to define behavior, to build something computational — we hand them a miniature on-screen QWERTY keyboard and say "type this syntax exactly right, semicolons and all."

We've made computing universal and programming exclusionary. In the same breath.

The emergence of touch-first, keyboard-optional computing isn't a trend. It's the settled reality. The question isn't whether programming will adapt. It's why it hasn't already.

## What Diagrams Get Right

Dataflow is not inherently sequential. A computation that filters a list, transforms each element, and reduces the result to a single value is a *graph* — a set of operations connected by data dependencies. Writing it as a sequence of lines imposes a false ordering. You have to read the whole function, mentally reconstruct the dependency graph, and hold it in your head.

A diagram just *shows* you the graph. The structure is the syntax. The layout is the logic.

This is what diagrammatic languages are built on. Visual programming with relational dataflow architecture. Programs are two-dimensional diagrams: nodes represent operations, terminals represent inputs and outputs, and wires represent the flow of data. There is no ambiguity about what depends on what. There is no invisible control flow hidden behind syntactic conventions. The diagram *is* the program, and the program *is* the diagram.

In a well-designed diagrammatic language, closures are first-class structural elements — compile-time constructs that dissolve into native control flow. Stacking nodes to express alternatives. Abutting nodes to form new constructs. The visual grammar maps directly to the computational semantics, without the indirection of parsing text into a tree, then interpreting the tree as a graph.

This is not a "node editor bolted onto a scripting language." This is a language designed from the ground up for two-dimensional expression, targeting native code.

## AI Changes the Equation

Here's where it gets interesting. AI code generation — the thing everyone is excited about right now — is fundamentally a text-to-text operation. You describe what you want in natural language (text), and an LLM produces source code (more text). It works because LLMs are extraordinary at manipulating token sequences.

But think about what that means for the *programmer*. Your job is increasingly to describe intent, review output, and verify behavior. You're not typing `for (int i = 0; i < n; i++)` — the machine does that. You're thinking about *what* should happen, *what* data flows where, and *what* the structure of the computation looks like.

That's a visual task. That's a diagrammatic task.

AI doesn't eliminate the need for programming languages. It eliminates the need for *typing* programming languages. And once typing is out of the picture, the entire rationale for text-based syntax collapses. What remains is the need to *see* the structure, *manipulate* the connections, and *understand* the flow. Diagrams do all three better than text ever has.

Imagine pointing an AI at a visual dataflow diagram and saying "add error handling to this pipeline" — and watching the diagram restructure itself in front of you. No diff to parse. No merge conflict in line 347. The change is spatially coherent and immediately visible.

## "But Visual Programming Has Been Tried Before"

Yes. And most attempts failed. But they failed for specific, identifiable reasons — not because the concept is flawed.

Early visual languages (LabVIEW, Simulink, Scratch) were either domain-specific, pedagogical, or limited to wiring together pre-built black boxes. They didn't offer the expressive power of a general-purpose language. They didn't compile to efficient native code. And they were designed in an era when every programmer had a keyboard, a large monitor, and no expectation of touch input.

None of those conditions hold anymore.

Diagrammatic programming is designed for the current moment: touch-capable hardware, AI-assisted development, LLVM-grade compilation, and a computational model — relational dataflow — that is inherently parallel and inherently visual. Closures-all-the-way-down architecture means that the visual grammar scales from trivial expressions to complex systems without devolving into spaghetti wiring diagrams.

The previous generation of visual programming failed because it brought a graphical skin to a textual paradigm. This visual approach starts from the diagram and builds the language around it.

## The Real Question

Every other engineering discipline made the jump from text to diagrams long ago. Electrical engineers don't describe circuits in prose. Architects don't write buildings as code. Mechanical engineers don't specify assemblies in paragraphs. They draw. They diagram. They work in two dimensions because two dimensions carry more information per unit of cognitive effort than one.

Software engineering is the holdout. The last discipline where "literacy" means the ability to read and write sequential text files. The last discipline where spatial reasoning — the most powerful cognitive tool humans possess — is deliberately excluded from the creative process.

Spock — the logical one, the one who would choose the optimal representation for any problem — would not code in ASCII. He would look at a text editor and ask why we are voluntarily discarding an entire spatial dimension of expression. He would find it... illogical.

Diagrammatic programming is an answer to that question. Not the only answer. But a serious one — built on a real compiler, a real computational model, and the real trajectory of how humans interact with machines.

The keyboards are leaving. The AI is arriving. The diagrams are waiting.

It's time.

---

