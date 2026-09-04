+++
date = '2026-09-01T13:22:35-03:00'
draft = false
title = 'Color Wheel'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "control-flow", "closures", "future-of-code", "programming-languages", "howto", "LLM"]
+++

Apologies for the gap since the last post. Creating a programming language is a taxing endeavor, but it can also be a lot of fun. The distraction this time: rewriting LQ in itself. Along the way, challenges surface — and those challenges tend to produce nice solutions.

This post is one of those solutions: a complete graphical application — a color wheel — written in LQ. Four methods, a handful of nodes, and a result that would take substantially more code in a text language. It also happens to be the first post in this series showing LQ producing graphical output rather than console text.

## The Application

The `main` method sets up the window and handles the event loop.

<figure style="text-align: center;">
    <img alt="Color wheel main method" width="55%" src="/blog/images/tutorial8.1.png">
    <figcaption>Color wheel <code>main</code> method</figcaption>
</figure>

The `runReactiveApp` expression node constructs a new window with the title "ColorWheel" and a size of 640×640, then invokes the abutted closure to handle events.

The `drawRing` method has a forEach input terminal, iterating through the range `0..4` — one invocation for each value `0, 1, 2, 3`, representing the four concentric rings. This is the iteration model from [Iteration Through Terminal Annotation](/blog/iteration-through-terminal-annotation) put to practical use: a single terminal annotation turns a method call into a loop.

## Drawing a Ring

<figure style="text-align: center;">
    <img alt="Color wheel drawRing method" width="55%" src="/blog/images/tutorial8.2.png">
    <figcaption>Color wheel <code>drawRing</code> method</figcaption>
</figure>

`drawRing` takes the ring number and computes two things from it: the distance from center (`0.32 + cast(ring) * 0.16`) and the lightness of the ring (`255 - band * 63`). Inner rings are lighter, outer rings are darker.

The angle of each wedge comes from the step range `0..360:6` — from 0 to 360 in increments of 6 degrees. That's 60 wedges per ring. The range feeds `drawWedge` through a forEach terminal, so each wedge is drawn in turn. The values passed to `drawWedge` are the window, the angle, the distance from center, and the lightness.

Two nested iterations — rings and wedges — and neither one uses a loop construct. The forEach terminals handle both.

## Drawing a Wedge

<figure style="text-align: center;">
    <img alt="Color wheel drawWedge method" width="60%" src="/blog/images/tutorial8.3.png">
    <figcaption>Color wheel <code>drawWedge</code> method</figcaption>
</figure>

`drawWedge` is where the geometry happens. The method converts the wedge angle from degrees to radians via `cast(deg)`, then computes two points using the `polar` method — one at the current angle and one at the angle plus 7 degrees (slightly overlapping to avoid gaps between wedges).

The two polar coordinates, along with the radius, feed into an SVG-style path string via interpolation: `"M 320 320 L \{x1} \{y1} A \{radius} \{radius} 0 0 1 \{x2} \{y2} Z"`. That path — a line from center to the first point, an arc to the second point, and back to center — defines the wedge shape.

The `fill` node constructs a paint that matches the hue angle color and ring lightness. The saturation is fixed at 0.9. Finally, `windowFillPath` fills the wedge with the paint.

## Polar Conversion

<figure style="text-align: center;">
    <img alt="Color wheel polar method" width="70%" src="/blog/images/tutorial8.4.png">
    <figcaption>Color wheel <code>polar</code> method</figcaption>
</figure>

The `polar` method is straightforward: take a radius and an angle in degrees, convert the angle to radians (`angle * pi() / 180.0`), then compute `x = 320.0 + radius * cos(angle)` and `y = 320.0 + radius * sin(angle)`. The two output parameters return the x and y coordinates.

Notice the parallelism — the `cos` and `sin` computations sit side by side with no dependency between them. Both feed from the same angle and radius inputs. In LQ, that independence is visible in the graph and means the two computations can run concurrently. In a text language, the same independence exists but is invisible — the reader has to verify it by reading both lines.

## The Result

<figure style="text-align: center;">
    <img alt="Color wheel window" width="65%" src="/blog/images/tutorial8.5.png">
    <figcaption>the color wheel — four rings, 60 wedges each, 240 filled paths</figcaption>
</figure>

Four methods. A few dozen nodes. The color wheel iterates through hues and lightness using nothing but ranges, forEach terminals, and basic trigonometry.

## What This Demonstrates

The color wheel is a small program, but it exercises a surprising range of LQ features:

**Iteration through terminal annotation.** Two nested loops — rings and wedges — expressed through forEach terminals on `drawRing` and `drawWedge`. No loop syntax. No counters. Just annotated terminals and ranges.

**Step ranges.** The `0..360:6` range produces 60 values at 6-degree increments. StepRange composes naturally with forEach — the iteration granularity is controlled by the range, not by the loop.

**Closures.** The `runReactiveApp` event handler is an abutted closure — the application's event loop expressed as a closure attached to the window constructor.

**String interpolation.** The SVG path string is assembled from computed coordinates using `\{...}` interpolation — the same feature introduced in [The LQ Type System](/blog/lq-type-system).

**Parallelism.** The `polar` method's `cos` and `sin` computations are visibly independent. The `drawRing` method's lightness and distance computations are independent. The graph shows the parallelism. The compiler can exploit it.

**Graphics.** LQ's UI library — built on Skia — handles window management, path construction, and HSL color fills. The library is the separate layer mentioned in the type system post, built on top of the core types.

Four methods, from event loop to pixel. No boilerplate. The graph is the program.
