+++
date = '2026-03-11T14:20:54-03:00'
draft = false
title = 'Let the Robots Speak'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "parallelism", "future-of-code", "programming-languages", "howto",  "LLM"]
+++

## Foreword

*I've been thinking about writing another diagrammatic language since my work on Prograph in the 1980s. Life happens — the time and resources to dive in never quite lined up. With the advent of AI, the possibility became a reality. Claude and I are best buds, and I've been able to do a brain dump of decades of ideas and actually watch them come together in a remarkably short period of time.*

*I hope the journey is interesting and insightful. The goal here isn't to peddle product — LQ is a perpetual prototype. This is more of a "here's what I've learned."*

*Time to show what's actually being built.*

## LQ

LQ is a visual dataflow programming language. Programs are graphs — nodes and connections — not text files. LQ compiles to native code through LLVM, has a real type system, real concurrency, and a real IDE with debugger. Not a flowchart tool. Not low-code. A language.

But the part that matters most right now: LQ is designed for AI agents.

Not "AI-assisted" — there's no copilot bolted onto a text editor. The language itself is the interface. An agent working in LQ doesn't serialize understanding into Python or TypeScript. The agent emits the graph directly — nodes, connections, types, data flow. The output matches how the agent already reasons about the problem.

No translation. No loss.

## A Worked Example

Here's a pattern every agent developer has written: take a query, search multiple sources in parallel, then pull the results together into a coherent answer.

In TypeScript, the code looks something like this:

```typescript
async function research(query: string): Promise<Response> {
  const [webResults, docResults, memoryResults] = await Promise.all([
    searchWeb(query),
    searchDocs(query),
    searchMemory(query)
  ]);

  const ranked = rankResults([...webResults, ...docResults, ...memoryResults]);
  return synthesize(query, ranked);
}
```

Fine. Functional. But look at the ceremony. An array destructuring to capture three parallel returns. A `Promise.all` wrapper because the language has no native way to say "these three things are independent." A spread-merge by hand. And none of the structure — the parallelism, the convergence, the data dependencies — is visible without reading every line.

Now here's the same thing in LQ:

<svg xmlns="http://www.w3.org/2000/svg" viewBox="50 10 414.98 270">
<g transform="scale(0.5)">
  <g id="connections">
    <path d="M 177.34 71.11 C 177.34 121.11, 243.50 152.77, 243.50 202.77" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 177.34 71.11 C 177.34 121.11, 353.50 152.77, 353.50 202.77" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 177.34 71.11 C 177.34 121.11, 475 152.77, 475 202.77" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 177.34 71.11 C 177.34 121.11, 187.18 325.02, 187.18 375.02" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 350.08 317.33 C 350.08 367.33, 216.85 325.02, 216.85 375.02" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 243.50 226.77 C 243.50 276.77, 326.33 243.33, 326.33 293.33" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 353.50 226.77 C 353.50 276.77, 350.08 243.33, 350.08 293.33" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 475 226.77 C 475 276.77, 373.83 243.33, 373.83 293.33" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 202.02 399.02 C 202.02 449.02, 206.93 445.99, 206.93 495.99" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
  </g>
  <g id="nodes">
    <g>
  <polygon points="177.34,57.11 170.34,71.11 184.34,71.11" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <circle cx="177.34" cy="71.11" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="197.50" y="202.77" width="92" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="243.50" y="218.77" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">searchWeb</text>
  <circle cx="243.50" cy="202.77" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="243.50" cy="226.77" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="305.50" y="202.77" width="96" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="353.50" y="218.77" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">searchDocs</text>
  <circle cx="353.50" cy="202.77" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="353.50" cy="226.77" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="417.50" y="202.77" width="115" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="475" y="218.77" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">searchMemory</text>
  <circle cx="475" cy="202.77" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="475" cy="226.77" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="302.58" y="293.33" width="95" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="350.08" y="309.33" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">rankResults</text>
  <circle cx="350.08" cy="317.33" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="326.33" cy="293.33" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="350.08" cy="293.33" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="373.83" cy="293.33" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="157.52" y="375.02" width="89" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="202.02" y="391.02" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">synthesize</text>
  <circle cx="187.18" cy="375.02" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="216.85" cy="375.02" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="202.02" cy="399.02" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <polygon points="206.93,509.99 199.93,495.99 213.93,495.99" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <circle cx="206.93" cy="495.99" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
  </g>
  </g>
</svg>

Same computation. But the parallelism isn't declared — the parallelism is *structural*. The three search nodes don't share any dependency between them, so all three run concurrently. That's not an optimization the compiler discovered. That's what the graph *means*. Visible at a glance.

The convergence into `rankResults` is just as obvious — three connections arriving at one node. No `Promise.all`. No array destructuring. No spread operator. The topology *is* the logic.

## Why This Matters for Agents

Text-based agent frameworks have a readability problem: when an agent writes a text program, understanding intent means reading the code line by line, mentally reconstructing the data flow, and hoping the agent made sensible assumptions.

When an agent writes an LQ graph, the *meaning is the picture*. A glance reveals: three sources searched in parallel, results merged. The agent's intent is the structure. Nothing to misread.

Debugging changes too. You can see the origins of incorrect data easily and because it is dataflow, you can rollback to the origin and watch how it went wrong.

Composition is straightforward. An agent building a larger system doesn't manage imports, namespaces, or module boundaries. Outputs connect to inputs. That's the whole model. The graph grows the way graphs grow — by adding nodes and wiring them in.

## Not What the Industry Has Seen Before

Anyone who's tried visual programming — LabVIEW, Scratch, Unreal Blueprints, node-based shader editors — probably has opinions. Most of those tools hit a wall: fine for small programs, falling apart at scale, or domain-specific, or just a GUI over text.

LQ is a general-purpose language. Closures, static typing, pattern matching, error handling — the machinery needed for real programs. The visual representation isn't a simplification of the underlying model. The visual representation *is* the model. There's no text file hiding behind the graph.

For now: the robots have been dreaming in dataflow. LQ is a language that lets them speak it.
