---
title: "Change the title of Table of Contents (ToC) in LaTeX"
date: 2017-09-13T00:00:00+02:00
draft: false
tags:
  - LaTeX
---

The steps to change the title of the *table of contents* (ToC) depends on if you are using the `babel` package or not.

## Without `babel`

The title of the *table of contents* can be changed using the command `\contentsname`. Let's see a small example:

```
\documentclass{article}
\renewcommand{\contentsname}{Index} % ToC will show "Index" instead of "Content"

\begin{document}

\tableofcontents

\section{Section}
\subsection{Subsection}

\end{document}
```

## With `babel`

When using `babel` package the name of the *table of contents* needs to be changed for the particular language used with `babel`.

The following example shows how to change the *ToC* name for *english* language:

```
\documentclass{article}

\usepackage[english]{babel}

\addto\captionsenglish{
  \renewcommand{\contentsname}
    {Index} % ToC will show "Index" instead of "Content"
}

\begin{document}

\tableofcontents

\section{Section}
\subsection{Subsection}

\end{document}
```

So, we can replace the " *english* " in `\captionsenglish` with the language we are using in `babel`.
