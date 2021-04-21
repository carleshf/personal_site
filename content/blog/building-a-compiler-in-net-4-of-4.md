---
title: "Building a Compiler in .Net  (4 of 4)"
date: 2012-04-01T00:00:00+02:00
tags:
  - .net
  - Csharp
---

This post is part of a series of 4. Here you have the links to each one:

* [Building a Compiler in .Net (1 of 4)]({{< ref "/blog/building-a-compiler-in-net-1-of-4" >}})
* [Building a Compiler in .Net (2 of 4)]({{< ref "/blog/building-a-compiler-in-net-2-of-4" >}})
* [Building a Compiler in .Net (3 of 4)]({{< ref "/blog/building-a-compiler-in-net-3-of-4" >}})
* [Building a Compiler in .Net (4 of 4)]({{< ref "/blog/building-a-compiler-in-net-4-of-4" >}})

---


## Introduction

In this forth post I am going to expose some utilities I have implemented for my version of VSLcompiler. I have designed them thinking in my first university's subject related with compilers.

I am tolking about three little tools. One on each "step" of VSLCompiler:


* A tokens viewer in Scanner.
* A tree viewer in Parser.
* A CIL viewer in Code-generation.

## Tokens viewer

Tokens viewer will allow users to see the output tokens list of Scanner. We power on tokens viewer with `-k` option on calling VSLCompiler.

Let's see this simple code:

```
var cnt = 0;
while cnt <> 5 do
    cnt = cnt + 1;
    print cnt;
endwhile;
```

The tokens viewer will show to user the tokens list below:

```
1. var
2. cnt
3. Assignment
4. 0
5. Semicolon
6. while
7. cnt
8. Differentiation
9. 5
10. do
11. cnt
12. Assignment
13. cnt
14. Addition
15. 1
16. Semicolon
17. print
18. cnt
19. Semicolon
20. endwhile
21. Semicolon
```

## Tree Viewer

Tree viewer allows us to see the representation of the syntactic tree of a given code. We can activate this option with the modifier `-t` on VSLCompiler's call.

The tree representation of the code used in tokens viewer's examples will be:

```
+-< VarStm >
   +-cnt
+-< Expr >
   +-< Bool >
      +-< Term >
         +-< NumberLt > 0
+-< WhileStm >
   +-< Expr > Differentiation
      +-< Bool >
         +-< Term >
            +-< Variable > cnt
      +-< Bool >
         +-< Term >
            +-< NumberLt > 5
   +-< AssignStmt >
      +-< Expr >
         +-< Bool > Addition
            +-< Term >
               +-< Variable > cnt
      +-< Term >
         +-< NumberLt > 1
   +-< PrintStm >
      +-< Expr >
         +-< Bool >
            +-< Term >
               +-< Variable > cnt
```

## CIL Viewer

This viewer gives us the ability to view the CIL code has been generated and that makes up our executable.

The CIL code generated to create the executable who implements de sampel code used in tokens viwer's examples is:

```
Offset OpCode Operand
0      ldc.i4       0
5      stloc.0
6      ldloc.0
7      ldc.i4       5
12     ceq
14     ldc.i4       0
19     beq      IL_00
22     ldc.i4       1
24     ldc.i4       0
29     br       IL_00
27     ldc.i4       0
34     ldc.i4       1
39     ldc.i4 0
44     beq      IL_00
44     ret
49     ldloc.0
50     ldc.i4       1
55     add
56     stloc.0
57     ldloc.0
58     call     System.Void System.Console::WriteLine(System.Int32)
63     br       IL_00
64    ldloc.0
68    ret
```

To implement this option I have used the assemblyes called `Mono.Cecil` and `Mono.Cecil.Cil` so, to use this option, you need to install them. If they are not installed in your system, this option will be disabled.

## Sources

VSLCompiler has been a little private project used to study for the subject called _Compilers 1_ (within Computer Science degree at Universitat Autònoma de Barcelona). Now I uploaded it to gitlab and I will make it a "public project". <del>I'm gonna create a page for the VSLCompiler here, in the blog, and I'm gonna post there the URL to gitlab's project, also the release version and a "to do list" for future versions.</del>


* [Here](https://gitlab.com/carleshf/vslcompiler/tree/master) you have the web page of VSLCompiler at gitlab.

