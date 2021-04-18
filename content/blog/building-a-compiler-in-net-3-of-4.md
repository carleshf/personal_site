---
title: "Building a Compiler in .Net  (3 of 4)"
date: 2012-02-13T00:00:00+02:00
tags:
  - .net
  - C#
---

This post is part of a series of 4. Here you have the links to each one:

* [Building a Compiler in .Net (1 of 4)]({{< ref "/blog/building-a-compiler-in-net-1-of-4" >}})
* [Building a Compiler in .Net (2 of 4)]({{< ref "/blog/building-a-compiler-in-net-2-of-4" >}})
* [Building a Compiler in .Net (3 of 4)]({{< ref "/blog/building-a-compiler-in-net-3-of-4" >}})
* [Building a Compiler in .Net (4 of 4)]({{< ref "/blog/building-a-compiler-in-net-4-of-4" >}})

---

## Introduction

### In previous posts

In this third post we will finish our compiler. In the last part ( _number 4_ ) we will talk about some educative improvements I've done to our compiler. So, now we're gonna talk about symbol's table, ".net machine code" and how to generate this one (not necessary in this order).

In the first post we said a compiler is formed, at last, by four different phases: Scanner, Parser, Semantic Analyser and Code Generation. In the second post we covered the implementation of the Scanner and the Parser. In this posts, the third we will implement a mix of Semantic Analyser and Code Generation.

Good compilers separate the Semantic Analyser of the generation of code to add a new stage: Code Optimization. This new step can be done after Semantic Analyser or after Code Generation. Our compiler, as simple as it is, will not have Code Optimization and, for this reason, we can mix Semantic Analyser with Code Generation.

### .NET Framework

We call Common Language Run time ( __CLR__ ) the virtual machine component of Microsoft .NET Framework responsible of the execution of .NET programs. It's in charge to compile the common Intermediate Language ( __CIL__ ) into machine code during the compilation process known as just-in-time ( __JIT__ ). CLR provides additional services related to memory management, type safety,... and an assembly language to define executable programs. All programs written for the .NET framework are executed by the CLR.

The CLR uses an abstract stack-based data structure to model code execution, and CIL, to define the operations you can perform on the stack. When a computer program defined using CIL is executed, the CLR simply simulates the operations specified against a stack, pushing and popping data to be executed instruction by instruction.

Let's see and example where we multiply two numbers (`2 * 3`):

```
ldc.i4 2
ldc.i4 3
mul
```

The two firsts instructions (`lcd.i4`) push an integer onto the stack. The last instruction (`mul`) multiplies the two pushed integers (popping them from the stack) and push the result onto the stack.

There are lots of CIL instructions available for building your programs they range from basic arithmetic to flow control to a variety of calling conventions. Details about all the CIL instructions can be found in [ecma-335](http://www.ecma-international.org/publications/standards/Ecma-335.htm).

Out compiler makes use of a Base Class Library component called `System.Reflection.Emit` to deal with IL code generation and .NET assembly creation and packaging. The `AssemblyBuilder` class is used to set up the necessary .NET assembly metadata elements like the manifest. The `ModuleBuilder` class is used to create modules within the assembly. `TypeBuilder` is used to create `Types` and their associated metadata. `MethodBuilder` and `LocalBuilder` deal with adding methods to types and locals to methods, respectively. The `ILGenerator` class is used to generate the CIL code for methods, utilizing the `OpCodes` class, which is a big enumeration containing all possible CIL instructions.

## Implementation

### Reflection.Emit

Our compiler relies heavily on the `Reflection.Emit` library to produce an executable. In the constructor of the class (shown behind) we set up the necessary infrastructure from `Reflection.Emit` to start generating code.

```csharp
public CodeGen( InstrStmt p_input, string p_module_name ) {
    if ( IO.Path.GetFileName( p_module_name ) != p_module_name ) {
        throw new System.Exception( "By the way we can only output into current directory!" );
    }
    Reflect.AssemblyName name = new Reflect.AssemblyName( IO.Path.GetFileNameWithoutExtension( p_module_name ) );
    Emit.AssemblyBuilder asmb = System.AppDomain.CurrentDomain.DefineDynamicAssembly( name, Emit.AssemblyBuilderAccess.Save );
    Emit.ModuleBuilder modb = asmb.DefineDynamicModule( p_module_name );

    // At start our SL language has no class loaded. But we need, at last, one class on root.
    Emit.TypeBuilder typeBuilder = modb.DefineType( "Foo" );

    // Here we defines a main method to hold the IL that will be generated.
    Emit.MethodBuilder methb = typeBuilder.DefineMethod( "Main", Reflect.MethodAttributes.Static, typeof( void ), System.Type.EmptyTypes );

    // Global CodeGenerator
    this.il = methb.GetILGenerator();
    this.symbol_table = new Collections.Dictionary< string, Emit.LocalBuilder >();

    // Compile
    this.GenInstr( p_input, null );

    il.Emit( Emit.OpCodes.Ret );
    typeBuilder.CreateType();
    modb.CreateGlobalFunctions();
    asmb.SetEntryPoint( methb ); // Calling SetEntryPoint on MethodBuilder so it will run on startup when a user runs the executable.
    asmb.Save( p_module_name );

    // Clear
    this.symbol_table = null;
    this.il = null;
    this.module_name = p_module_name;
}
```

We start defining the assembly name and passing that to the assembly builder. In this example, we use the source file name as the assembly name. Next is the `ModuleBuilder` definition this uses the same name as the assembly. After that we define a `TypeBuilder` on the `ModuleBuilder` to hold the only type in the assembly. There are no types defined as first class citizens of the our language definition, but at least one type is necessary to hold the method, which will run on startup. We cal it _Foo_. The `MethodBuilder` defines a Main method to hold the CIL that will be generated. We need to call `SetEntryPoint` on this `MethodBuilder` so it will run on startup when the executable will be runt. The we create the global `ILGenerator` from the `MethodBuilder` using the `GetILGenerator` method.

### GenInstr

After all this set up step we call the function `GenInstr`. This function will walk into the syntax-tree generating the necessary CIL code using our `ILGenerator` instance.

```csharp
private void GenInstr(object p_instr, System.Type p_expected_type) { 

    System.Type my_type = null;

    if ( p_instr is PrintStmt ) {
    }
    else if ( p_instr is Sequence ) {
    }
    else if ( p_instr is AssignStmt ) {
    }
    else if ( p_instr is VarStmt ) {
    }
    else if ( p_instr is ReadIntStmt ) {
    }
    else if ( p_instr is ReadStrStmt ) {
    }
    else if ( p_instr is IfStmt ) {
    }
    else if ( p_instr is WhileStmt ) {
    }
    else if ( p_instr is ExprStmt ) {
    }
    else if ( p_instr is BoolStmt ) {
    }
    else if ( p_instr is TermStmt ) {
    }
    else if ( p_instr is StringLt ) {
    }
    else if ( p_instr is NumberLt ) {
    }
    else if ( p_instr is BracketExpr) {
    }
    else if ( p_instr is Variable ) {
    }
    else {
        throw new System.Exception( "Unexpected code bloc " + p_instr.GetType().Name );
    }

    if (p_expected_type != null && my_type != null && my_type != p_expected_type ) {
        if ( my_type == typeof( int ) && p_expected_type == typeof( string ) ) {
            this.il.Emit( Emit.OpCodes.Box, typeof( int ) );
            this.il.Emit( Emit.OpCodes.Callvirt, typeof( object ).GetMethod( "ToString" ) );
        } 
        else {
            throw new System.Exception( "Can't coerce a " + my_type.Name + " to a " + p_expected_type.Name );
        }
    }
}
```

As we can see each type of "VSL instruction" has its own code-generation step. Now we're going to look carefully three of this steps, you can look the other ones at your own time. The three cases discussed below represent the three different types of cases that we are generating code: Code-Generation code in intself, check/modify the Symbol's table and using existing methods.

#### `NumberLt`

Let's start with `NumberLt`:

```csharp
else if ( p_instr is NumberLt ) { 
    this.il.Emit( Emit.OpCodes.Ldc_I4, ( (NumberLt) p_instr ).Value ); my_type = typeof( int ); 
}
```

In this case we're creating the opcode `lcd.i4` like the opcode seen in the .NET Framework section of this article, pushing to the stack the value of a number literal.

### `Variable`

Now we go with `Variable`:

```csharp
else if ( p_instr is VarStmt ) { 
    VarStmt declare = (VarStmt) p_instr; 
    if ( !this.symbol_table.ContainsKey( declare.Identifier ) ) { 
        // declare local 
        this.symbol_table[ declare.Identifier ] = this.il.DeclareLocal( this.TypeOf( declare.Expression ) ); 
        // set the initial value => < identifier > = < expr > AssignStmt assign = new AssignStmt(); 
        assign.Identifier = declare.Identifier; 
        assign.Expression = declare.Expression; this.GenInstr( assign, null ); 
    } 
    else { 
        throw new System.Exception( "A variable with name "" + declare.Identifier + "" was declared before." ); 
    } 
} 
// [ ... ] 
else if ( p_instr is AssignStmt ) { 
    AssignStmt assign = (AssignStmt) p_instr; 
    this.GenInstr( assign.Expression, null ); 
    this.StoreTS( assign.Identifier, this.TypeOf( assign.Expression ) ); 
}
```

In this case we're creating a variable. First of all we must check if a variable with the same name exists. If not, we add this new variable to the symbol's table with it's type. Then we create an assignment statement to store the first value of the declared variable.

### `PrintStmt`

The last case of code-generation we're gonna see is `PrintStmt`:

```csharp
if ( p_instr is PrintStmt ) { 
    // "print" statement is an alias for System.Console.WriteLine. 
    // it uses the string case System.Type 
    expr_type = this.TypeOf( ( (PrintStmt) p_instr ).Expression ); 
    this.GenInstr( ( (PrintStmt) p_instr ).Expression, null ); 
    this.il.Emit( Emit.OpCodes.Call, typeof( System.Console ).GetMethod( "WriteLine", new System.Type[] { expr_type } ) ); 
}
```

In this last case we call a function from `System.Console` class. See we use `Emit.OpCode.Call` with a `GetMethod` to access to method `WriteLine`.
