+++
title = "Editing Tikz Axis"
date = 2025-05-03
draft = false

[taxonomies]
categories = ["Software"]
tags = ["latex", "tikz"]

[extra]
toc = true
keywords = "Latex tikz axis editing scale log plot xaxis yaxis"
math = true
+++

One of the problems which I struggled a lot with Tikz was trying to scale the axis of a plot, as it would end up choosing the most obscure scales like $10^2$.

<!--more-->

So, I compiled all my fixes that I found, which turns a graph like:
{{
    img(src="./tikz-graph-broken-axis.png" class="ci" alt="Tikz graph with the y axis having a combination of 10^-2 and 0.1, with the x axis overscaled")
}}


_(Seriously, Tikz what is going on here!? -- there are label collisions, weird scales)_

Into a graph like:
{{
    img(src="./tikz-graph-fixed-axis.png" class="ci" alt="Nice clean Tikz graph")
}}
This involves editing the _"frontmatter"_ of the tikz file to force it to change scales.

This is what my old front-matter looked like:
```latex,name=Tikz
\begin{axis}[%
    width=4.521in,
    height=3.566in,
    at={(0.758in,0.481in)},
    scale only axis,
    xmin=0,
    xmax=1000,
    ymin=0.02,
    ymax=0.16,
    axis background/.style={fill=white},
    xminorgrids,
    yminorgrids
]
```

With the config telling Tikz the ranges of the $x$ and $y$ axis, but importantly nothing about the scale. (The "scale only axis" prevents the graph from warped).

To fix this we need to force Tikz to scale for us,
```tikz,name=Tikz
scaled x ticks=base 10:-3,
scaled y ticks=base 10:3,
```
These functions basically times every $x$ tick value by $10^{-3}$ and every $y$ tick value by $10^{3}$, which shifts into more respectable ranges. To stop a little $10^{3}$ from floating around next to the axis, we can overload tikz to remove that pesky little number:
```latex,name=Tikz
xtick scale label code/.code={},
ytick scale label code/.code={},
```
This basically forces the little scale label to be blank.

So, this is what the final front-matter looks like:
```latex,name=Tikz
\begin{axis}[%
width=4.521in,
height=3.566in,
at={(0.758in,0.481in)},
scale only axis,
xmin=0,
xmax=1000,
ymin=0.02,
ymax=0.16,
axis background/.style={fill=white},
xminorgrids,
yminorgrids,
xlabel={Days $\times10^3$},
ylabel={Tangerines eaten $\times10^3$},
scaled y ticks=base 10:3,
ytick scale label code/.code={},
scaled x ticks=base 10:-3,
xtick scale label code/.code={}
]
```

And doing logarithmic scales is very similar,
```latex,name=Tikz
xmode=log,
ymode=log,
```

So, there you go!


#### P.S., making these graphs
I made these graphs through importing CSV data into MatLab and then using the `matlab2tikz` package to export the data into a Tikz file. 