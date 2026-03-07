+++
date = '2026-03-07T12:35:51-04:00'
draft = false
title = 'Maps'
author = 'Jim Laskey'
tags = ["programming", "coding", "ai", "claude", "dataflow", "languages", "visual-programming", "parallelism", "future-of-code", "programming-languages", "manifesto",  "LLM"]
+++

This method constructs a map and performs operations on that map. Since LQ objects are immutable, put and remove create new maps as one of the outputs.

<svg xmlns="http://www.w3.org/2000/svg" viewBox="-4 -4 346.61 320">
<g transform="scale(0.5)">
  <g id="connections">
    <path d="M 67.77 88 C 67.77 138, 117.77 96, 117.77 146" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 187.77 88 C 187.77 138, 137.77 96, 137.77 146" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 127.77 170 C 127.77 220, 46 186.63, 46 236.63" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 127.77 170 C 127.77 220, 242.95 186.63, 242.95 236.63" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 252.95 260.63 C 252.95 310.63, 253.77 283.19, 253.77 333.19" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 292.61 192.93 C 292.61 242.93, 262.95 186.63, 262.95 236.63" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 173.29 417.38 C 173.29 467.38, 173.77 430.56, 173.77 480.56" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 251.11 417.38 C 251.11 467.38, 193.77 430.56, 193.77 480.56" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 173.77 504.56 C 173.77 554.56, 175.26 528.43, 175.26 578.43" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 127.77 170 C 127.77 220, 64.49 343.38, 64.49 393.38" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 125.77 347.32 C 125.77 397.32, 87.49 343.38, 87.49 393.38" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
    <path d="M 75.99 417.38 C 75.99 467.38, 153.77 430.56, 153.77 480.56" fill="none" stroke="#3D6699" stroke-width="1" stroke-linecap="round"/>
  </g>
  <g id="nodes">
    <g>
  <rect x="37.77" y="16" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="67.77" y="32" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">&quot;a&quot;</text>
    </g>
    <g>
  <rect x="157.77" y="16" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="187.77" y="32" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">1</text>
    </g>
    <g>
  <rect x="37.77" y="40" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="67.77" y="56" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">&quot;b&quot;</text>
    </g>
    <g>
  <rect x="157.77" y="40" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="187.77" y="56" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">2</text>
    </g>
    <g>
  <rect x="37.77" y="64" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="67.77" y="80" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">&quot;c&quot;</text>
  <circle cx="67.77" cy="88" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="157.77" y="64" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="187.77" y="80" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">3</text>
  <circle cx="187.77" cy="88" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="97.77" y="146" width="60" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="127.77" y="162" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">map</text>
  <circle cx="117.77" cy="146" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="137.77" cy="146" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="127.77" cy="170" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="262.61" y="168.93" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="292.61" y="184.93" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">&quot;b&quot;</text>
  <circle cx="292.61" cy="192.93" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="16" y="236.63" width="60" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="46" y="252.63" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">println</text>
  <circle cx="46" cy="236.63" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="222.95" y="236.63" width="60" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="252.95" y="252.63" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">get</text>
  <circle cx="242.95" cy="236.63" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="262.95" cy="236.63" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="252.95" cy="260.63" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="95.77" y="323.32" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="125.77" y="339.32" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">&quot;b&quot;</text>
  <circle cx="125.77" cy="347.32" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="223.77" y="333.19" width="60" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="253.77" y="349.19" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">println</text>
  <circle cx="253.77" cy="333.19" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="41.49" y="393.38" width="69" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="75.99" y="409.38" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">remove</text>
  <circle cx="64.49" cy="393.38" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="87.49" cy="393.38" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="75.99" cy="417.38" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="143.29" y="393.38" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="173.29" y="409.38" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">&quot;d&quot;</text>
  <circle cx="173.29" cy="417.38" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="221.11" y="393.38" width="60" height="24" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <text x="251.11" y="409.38" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">44</text>
  <circle cx="251.11" cy="417.38" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="133.77" y="480.56" width="80" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="173.77" y="496.56" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">put</text>
  <circle cx="153.77" cy="480.56" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="173.77" cy="480.56" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="193.77" cy="480.56" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
  <circle cx="173.77" cy="504.56" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
    <g>
  <rect x="145.26" y="578.43" width="60" height="24" rx="8" fill="#DAE4F2" stroke="#3D6699" stroke-width="1"/>
  <text x="175.26" y="594.43" font-family="system-ui, -apple-system, sans-serif" font-size="13" fill="#3D6699" text-anchor="middle">println</text>
  <circle cx="175.26" cy="578.43" r="4" fill="#FFFFFF" stroke="#3D6699" stroke-width="1"/>
    </g>
  </g>
</g>
</svg>

```
[a: 1, b: 2, c: 3]
2
[a: 1, c: 3, d: 44]
jlaskey
```
