+++
date = '2026-03-16T10:03:50-03:00'
draft = false
title = 'The LQ Tool Chain'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "parallelism", "future-of-code", "programming-languages", "howto",  "LLM"]
+++

[Let the Robots Speak](/blog/let-the-robots-speak) introduced LQ — a visual dataflow language designed for AI agents. This post looks under the hood at how LQ programs go from graph to running code.

## Architecture

The LQ toolchain is built around a single persistent process: the **lqc server**. Both the native macOS IDE and (eventually) a browser-based client communicate with lqc through a message-based protocol. One server, multiple clients, same capabilities.

<svg xmlns="http://www.w3.org/2000/svg" viewBox="0 20 200 100">
  <style>
    .wire { fill: none; stroke: black; stroke-linecap: round; stroke-width: 1; }
    .box { fill: #e8f2fd; stroke: black; stroke-linecap: round; stroke-width: 1; }
    .container { fill: #fafcff; stroke: black; stroke-linecap: round; stroke-width: 1; }
    .title { font-family: Arial, sans-serif; font-size: 12px; fill: black; text-anchor: middle; }
    .sub { font-family: Arial, sans-serif; font-size: 12px; fill: black; text-anchor: middle; }
  </style>
  <!-- Wires: clients to server -->
  <g transform="scale(0.20)">
  <g class="wire">
    <line x1="261.40" y1="215.80" x2="337.95" y2="272.84"/>
    <line x1="417.43" y1="217.13" x2="416.92" y2="272.84"/>
    <line x1="602.93" y1="217.63" x2="512.31" y2="272.84"/>
    <line x1="348.75" y1="389.71" x2="285.32" y2="444.50"/>
    <line x1="478.35" y1="389.71" x2="529.23" y2="437.70"/>
  </g>
  <!-- Clients -->
  <rect class="box" x="126.04" y="153.80" width="187.50" height="62" rx="8"/>
  <text class="title" x="219.79" y="181">IDE</text>
  <text class="sub" x="219.79" y="196">Native MacOS</text>
  <rect class="box" x="324.73" y="153.80" width="187.50" height="62" rx="8"/>
  <text class="title" x="418.48" y="181">AI Agent</text>
  <text class="sub" x="418.48" y="196">MCP</text>
  <rect class="box" x="523.43" y="153.80" width="187.50" height="62" rx="8"/>
  <text class="title" x="617.18" y="181">Browser</text>
  <text class="sub" x="617.18" y="196">(pending)</text>
  <!-- lqc server container -->
  <rect class="container" x="102.38" y="272.84" width="628.03" height="116.87" rx="8"/>
  <text class="title" x="416.39" y="289">lqc server</text>
  <!-- Language Service -->
  <rect class="box" x="121.86" y="300.27" width="187.50" height="62" rx="8"/>
  <text class="title" x="215.61" y="321">Language Service</text>
  <text class="sub" x="215.61" y="336">Autocomplete</text>
  <text class="sub" x="215.61" y="350">Verify</text>
  <!-- Compiler -->
  <rect class="box" x="322.64" y="300.27" width="187.50" height="62" rx="8"/>
  <text class="title" x="416.39" y="328">Compiler</text>
  <text class="sub" x="416.39" y="343">Compile, Build, Bundle</text>
  <!-- Runtime -->
  <rect class="box" x="523.43" y="300.27" width="187.50" height="62" rx="8"/>
  <text class="title" x="617.18" y="321">Runtime</text>
  <text class="sub" x="617.18" y="336">Run</text>
  <text class="sub" x="617.18" y="350">Debug</text>
  <!-- LLVM -->
  <rect class="box" x="155.68" y="444.50" width="187.50" height="62" rx="8"/>
  <text class="title" x="249.43" y="472">LLVM</text>
  <text class="sub" x="249.43" y="487">Native Code Generation</text>
  <!-- LLDB -->
  <rect class="box" x="468.34" y="437.70" width="187.50" height="62" rx="8"/>
  <text class="title" x="562.09" y="465">LLDB</text>
  <text class="sub" x="562.09" y="480">Debug Engine</text>
  </g>
</svg>

The lqc server handles three categories of work: language services, compilation, and runtime.

## Language Services

The language service is the real-time layer — the part that responds while the developer (or agent) is still editing.

**Autocomplete** examines the current graph context — the types flowing through nearby connections, the available methods on those types — and suggests completions. Because LQ programs are typed graphs rather than text files, completions are structurally constrained. The system doesn't guess what token might come next in a stream of characters. Instead, the system knows what types arrive at a terminal and offers only the methods that accept those types.

**Verification** runs continuously in the background, checking the graph for type mismatches, disconnected terminals, unreachable closures, and structural/expression errors. Diagnostics are reported with node and terminal IDs, so the IDE can highlight exactly where the problem is — not a line number, but a specific node in the graph.

## Compiler

When the graph is ready to build, the compiler takes over.

**Compilation** translates the LQ graph into LLVM IR. This is where the dataflow semantics become concrete: independent branches in the graph map to parallel execution paths, data dependencies become explicit in the IR, and the type system is fully resolved. LLVM then handles optimization and native code generation for the target platform.

**Building** links compiled modules into executables or libraries. Because the code generation and libraries are all in LLVM bitcode, optimization (ex. inlining) can span compile units.

**Bundling** packages the result for deployment — whether that's a standalone binary, a library, or an agent-consumable artifact.

The choice of LLVM is deliberate. LQ programs compile to the same native code that C, C++, Rust, and Swift produce. No interpreter. No VM. No runtime overhead from the visual representation — the graph is a compile-time construct that dissolves into efficient machine code.

## Runtime

**Running** an LQ program launches the compiled binary under the server's supervision, with the ability to stream output and status back to the IDE.

**Debugging** uses LLDB — the same debug engine behind Xcode and the LLVM project. Breakpoints, stepping, variable inspection — all mapped back to nodes and connections in the graph rather than lines in a text file. When execution pauses, the IDE highlights the active node and shows data values on the connections flowing into and out of it.

This is one of the advantages of a graph-based representation for debugging. A text debugger shows a call stack — a linear trace through nested function calls. An LQ debugger shows the *topology* — which branches are active, which are waiting, where data has flowed and where it hasn't. The state of the program is visible as the state of the graph.

## One Server, Any Client

The lqc server doesn't care what's on the other end of the message protocol. Today that's a native macOS IDE built in SwiftUI. Tomorrow it could be a browser-based editor, a command-line tool, or an AI agent sending messages programmatically.

This is the same pattern that made LSP (Language Server Protocol) successful — decouple the language intelligence from the editor. The difference is that LQ's protocol is richer than LSP because the language is richer than text. Verify doesn't return squiggly underlines on a line range — it returns diagnostics on specific nodes and terminals. Autocomplete doesn't suggest text insertions — it suggests typed method invocations with compatible signatures.

The protocol is the contract. The client is interchangeable.

## What's Next

Upcoming posts will dig deeper into specific layers — the type system, how closures work in a dataflow graph, the agent serialization format that lets AI write LQ programs directly, and how control flow emerges from combining simple visual primitives.

The toolchain is the foundation. Everything else builds on top of it.
