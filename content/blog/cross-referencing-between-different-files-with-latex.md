---
title: "Cross-referencing between different files with LaTeX"
date: 2017-09-28T00:00:00+02:00
draft: false
tags:
  - LaTeX
---

Sometimes I want to refer labels I placed in external `.tex` files. I mean, I want o refer  (using the command `\ref`) a `\label` from  a `.tex` file I created in another project.

A valid solution it to use the command `\include`. But I in this case I don't want to include the `.tex`, I only want to be able to refer their labels.

This can be done using the `xr` package. The command `\externaldocument` allows to _include_ an external `.tex` file (without adding its content to the current file) and use `\ref` like normal.

For example, being in `my_doc.tex` I want o refer a label in `../thesis/thesis.tex`. We add into the preamble:

```
\usepackage{xr}
\externaldocument{../thesis/thesis}
```

And then, in the document we can do `\ref{chp1:f:miRNA-mechanism}`.

__WARNING__: The package looks for the `aux` file, so it does not work in all settings and, more importantly, without a full document. So, if I want to refer the labels in `chapter1.tex`, that is added to `thesis.tex` with an `\include`, I still need to run the `\externaldocument{../thesis/thesis}` instead of `\externaldocument{../thesis/chapter1}`.
