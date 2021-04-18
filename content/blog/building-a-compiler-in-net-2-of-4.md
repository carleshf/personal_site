---
title: "Building a Compiler in .Net  (2 of 4)"
date: 2012-01-25T00:00:00+02:00
draft: false
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

In this second post we will implement the two first steps of a compiler: Scanner and Parser. Remember from part 1 a Scanner, also known as Lexical analyser, reads the characters from source code file and compose tokens. This tokens will be taken by Parser (or Syntax Analyser) and will validate it's order using a grammar, generating the syntax-tree.

### Very Simple Language

It seems a good moment to write some lines about the language we will be able to compile. It's called _Very Simple Language_ (VSL for friends). We will be able to declare variables without specifying it's type and make assignments, make conditional statements (`if-then-else`), loops (`while`), show information of screen (`print`) and read from console (`read_int` and `read_str`). As you can see we will not have functions or procedures, neither dynamic variable. I think it's the minimum expression of a "hight-level programming language".

The grammar for __VSL__ is:

```
< instr_stmt > ::=
    | var < identifier > = < expr_stmt >
    | < identifier > = < expr_stmt >
    | read_int < identifier >
    | read_str < identifier >
    | print < expr_stmt >
    | < instr_stmt >; < instr_stmt >
    | if < expr_stmt > then < instr_stmt > { else < instr_stmt > } endif
    | while < expr_stmt > do < instr_stmt > endwhile

< expr_stmt > ::= < bool_stmt > { < bool_op > < bool_stmt > }

< bool_op > ::= == | <>
< bool_stmt > ::= < term_stmt > { < term_op > < term_stmt > }

< term_op > ::= + | -
< term_stmt > ::= < factor_stmt > { < factor_op > < factor_stmt > }

< factor_op > ::= * | /
< factor_stmt > ::=
    | < variable >
    | < number_lt >
    | < string_lt >
    | ( < expr_stm > )

< variable > ::= < char > < ident_rest >*
< ident_rest > ::= < char > | < digit >

< number_lt > ::= < digit >+
< digit > ::= 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9

< string_lt > ::= " < string_elem >* "
< string_elem > ::= < any char other than " >
```

Take care with the grammar. A good grammar will be longer than this one: Or numbers can't be negative, we haven't specified how big the numbers can be (32 bit integer?). A true and complete BNF grammar definition would precisely define all these details. By the way, in our VSLCompiler v0 this will be our grammar (If I have time, in the future, I will try to improve the grammar and the compiler for VSL).

Here we can see a sample of a VSL program:

```
var times = 0;
print "Type the number:";
read_int times;

var cnt = 0;
while cnt <> times do
    print "lambda-function";
    cnt = cnt + 1;
endwhile;
```

## Implementation

### Scanner

The primary job of the Scanner is to break the input text in tokens. It's in charge to determinate which tokens will be sent to the Parser so it's able to throw out things that are not defined in the grammar (like tabs, comments,...).

So, at the end, we need to define with symbols will be interpreted as operands (in VSL case: `+`, `-`, `*`, `/`, `=`, `==`, `<>`) or special operands (`(`, `)`, `;`):

```csharp
public enum tokens { Addition, Substraction, Division, Production, Assignment, Equating, Differentiation, LBracket, RBracket, Semicolon };
```

The Scanner implemented for VSL is as simple as the language. The structure of the class is the following one:

```csharp
public class Scanner {

    public enum tokens { 
        Addition, Substraction, Division, Production, 
        Assignment, Equating, Differentiation,
        LBracket, RBracket, Semicolon 
    };

    public Collections.IList<object> Tokens {
        get { return this.result; }
    }

    private void Scan( IO.TextReader p_input ) {
        // TODO
    }
    </code></pre>

}
```

With this structure we get an "input text stream" with a `TextReader` object and we will process it in `Scan` method. This method is, basically, a loop that walks over every character trying to recognise it according to the grammar. Every time a character is recognised a token will be formed and pushed into the tokens-list `result`. We assume that when we find a character `"` it will encapsulate a string token.

```csharp
private void Scan( IO.TextReader p_input ) { 
    while ( p_input.Peek() != -1 ) { 
        char read_ch = (char) p_input.Peek();
        if ( char.IsWhiteSpace( read_ch ) ) {
            // Skiping white spaces
            p_input.Read();
        }
        else if ( char.IsLetter( read_ch ) || read_ch == '_' ) {
            // Reading an identifier
            Text.StringBuilder accum = new Text.StringBuilder();
            while ( char.IsLetter( read_ch ) || read_ch == '_' ) {
                accum.Append( read_ch );
                p_input.Read();

                if ( p_input.Peek() == -1 ) {
                    break;
                }

                read_ch = (char) p_input.Peek();
            }
            this.result.Add( accum.ToString() );
        }
        else if ( read_ch == '"' ) {
            // Reading a string literal
            Text.StringBuilder accum = new Text.StringBuilder();
            p_input.Read(); // Skiping "

            do {
                if ( p_input.Peek() == -1 ) {
                    throw new System.Exception("Unterminated string literal.");
                }
                read_ch = (char) p_input.Peek();
                if ( read_ch != '"' ) {
                    accum.Append( read_ch );
                    p_input.Read();
                }
            } while ( read_ch != '"' );
            p_input.Read();
            this.result.Add( accum ); // NO .ToString(). We will use type TextBuffer in Parser.
        }
        else if ( char.IsDigit( read_ch ) ) {
            // Reading a numeric literal
            Text.StringBuilder accum = new Text.StringBuilder();
            bool is_float = false;
            while( char.IsDigit( read_ch ) || ( read_ch == '.' ) ) {
                accum.Append( read_ch );
                p_input.Read();

                if ( p_input.Peek() == -1 ) {
                    break;
                }

                if ( read_ch == '.' ) {
                    is_float = true;
                }

                read_ch = (char) p_input.Peek();
            }

            if ( is_float ) {
                this.result.Add( float.Parse( accum.ToString() ) );
            }
            else {
                this.result.Add( int.Parse( accum.ToString() ) );
            }
        }
        else if ( read_ch == '+' ) {
            p_input.Read();
            this.result.Add( Scanner.tokens.Addition );
        }
        else if ( read_ch == '-' ) {
            p_input.Read();
            this.result.Add( Scanner.tokens.Substraction );
        }
        else if ( read_ch == '*' ) {
            p_input.Read();
            this.result.Add( Scanner.tokens.Production );
        }
        else if ( read_ch == '/' ) {
            p_input.Read();
            this.result.Add( Scanner.tokens.Division );
        }
        else if ( read_ch == '=' ) {
            p_input.Read();
            if ( ( p_input.Peek() != -1 ) && ( p_input.Peek() == '=' ) ) {
                p_input.Read();
                this.result.Add( Scanner.tokens.Equating );
            }
            else {
                this.result.Add( Scanner.tokens.Assignment );
            }
        }
        else if ( read_ch == '<' ) {
            p_input.Read();
            if ( ( p_input.Peek() == -1 ) || ( p_input.Peek() != '>' ) ) {
                throw new System.Exception("Unterminated operation literal.");
            }
            else {
                p_input.Read();
                this.result.Add( Scanner.tokens.Differentiation );
            }
        }
        else if ( read_ch == ';' ) {
            p_input.Read();
            this.result.Add( Scanner.tokens.Semicolon );
        }
        else if ( read_ch == '(' ) {
            p_input.Read();
            if ( ( p_input.Peek() != -1 ) && ( p_input.Peek() == '*' ) ) {
                p_input.Read();
                bool end_comm = false;
                do {
                    if ( p_input.Peek() == -1 ) {
                        throw new System.Exception("Unterminated comment.");
                    }
                    read_ch = (char) p_input.Peek();
                    p_input.Read();
                    if ( ( read_ch == '*' ) && (p_input.Peek() == ')') ) {
                        p_input.Read();
                        end_comm = true;
                    }
                } while ( !end_comm );
                p_input.Read();
            }
            else {
                this.result.Add( Scanner.tokens.LBracket );
                if ( p_input.Peek() == -1 ) {
                    throw new System.Exception("Unterminated operation.");
                }
            }
        }
        else if ( read_ch == ')' ) {
            p_input.Read();
            this.result.Add( Scanner.tokens.RBracket );
        }
    }
}
```

At this point we have all the tokens in `result`, ready to be sent to Parser.

### Parser

The Parser developed for Very Simple Language has a umber of jobs: The main one is to ensure source program conforms to the grammar specification (if not it raises an error). It also creates the syntax-tree, the unique representation of the source code, consumed by Code Generator, and figures out what runtime types to use.

On the first hand we are going to take a look the the syntax-tree structure. In my mind I have developed the syntax-tree as a instanced-class tree, lets see:

```csharp
public abstract class InstrStmt { }

// var < identifier > = < expr_stmt > 
public class VarStmt : InstrStmt { public string Identifier; public ExprStmt Expression; }

// < identifier > =  
public class AssignStmt : InstrStmt { public string Identifier; public ExprStmt Expression; }

// read_int < identifier > 
public class ReadIntStmt : InstrStmt { public string Identifier; }

// read_str < identifier > 
public class ReadStrStmt : InstrStmt { public string Identifier; }

// print < expr_stmt > 
public class PrintStmt : InstrStmt { public ExprStmt Expression; }

// < instr_stmt >; < instr_stmt > 
public class Sequence : InstrStmt { public InstrStmt First; public InstrStmt Second; }

// if < expr_stmt > then < instr_stmt > { else < instr_stmt > } endif 
public class IfStmt : InstrStmt { public ExprStmt Condition; public InstrStmt If_Body; public InstrStmt Else_Body; }

// while < expr_stmt > do < instr_stmt > endwhile 
public class WhileStmt : InstrStmt { public ExprStmt Condition; public InstrStmt While_Body; }

// < expr_stmt > = < bool_stmt > {< bool_op > < bool_stmt > } 
public class ExprStmt { public BoolStmt Right; public BoolStmt Left; public BoolOp Op; }

// < bool_op > ::= == | <> 
public enum BoolOp { Equating, Differentiation };

// < bool_stmt > ::= < term_stmt > { < term_op > < term_stmt > } 
public class BoolStmt { public TermStmt Right; public TermStmt Left; public TermOp Op; }

// < term_op > := + | - 
public enum TermOp { Addition, Substraction };

// < term_stmt > ::= < factor_stmt > { < factor_op > < factor_stmt > } 
public class TermStmt { public FactorStmt Right; public FactorStmt Left; public FactorOp Op; }

// < factor_op > ::= * | / 
public enum FactorOp { Division, Production };

// < factor_stmt > ::= < identifier > // | < number > // | < string > // | ( < expr_stm > ) 
public abstract class FactorStmt { }

// < string > ::= " < string_elem >* " 
public class StringLt : FactorStmt { public string Value; }

// < int > ::= < digit >+ 
public class IntLt : FactorStmt { public int Value; }

public class FloatLt : FactorStmt { public float Value; }

// < ident > ::= < char > < ident_rest >* // < ident_rest > ::= < char > | < digit > 
public class Variable : FactorStmt { public string Identifier; }

// ( < expr_stm > ) 
public class BracketExpr : FactorStmt { public ExprStmt Expression; }
```

I prefer to think syntax-tree keeps the structure of the source code according to the language's grammar. Sure you can find a different way to implement the syntax-tree (may be an abstract syntax-tree).

There are many algorithms available for parsing implementation. The definition of our grammar implies we will use a LL strategy (Left-to-right, Left-most derivation) and a top-down parser (for more information you can see LR, and bottom-up grammars). At the end, LL, simple means that it reads text from left to down, building the syntax-tree based on the next available token.

The constructor for my Parser class is as simply as Scanner ones. It takes a list of tokens that was created by the scanner:

```csharp
public class Parser {

    private int index;
    private Collections.IList<object> tokens;
    private readonly InstrStmt result;

    public Parser ( Collections.IList<object> p_tokens ) {
        this.tokens = p_tokens;
        this.index = 0;
        this.result = this.ParseInstr();

        if (this.index != this.tokens.Count) {
            throw new System.Exception("Expected EOF");
        }
    }

    public InstrStmt Result {
        get { return result; }
    }

    private InstrStmt ParseInstr() {
        // TODO
    }

    private ExprStmt ParseExpr() {
        // TODO
    }

    private BoolStmt ParseBool() {
        // TODO
    }

    private TermStmt ParseTerm() {
        // TODO
    }

    private FactorStmt ParseFctr() {
        // TODO
    }
}
```

The core of the parsing work is done by `ParseInstr` method. As we can see in the code before it returns a `InstrStmt` node (which serves as a root node of the syntax-tree). The Parser traverses the list of tokens using an index as the current position. For each token it tries to assign to an "Instruction case", in other case it raises an error. In each "instruction case" the `ParseInstr` will analyses the statement using the other Parsing methods (`ParseExprs`, `ParseBool`, `ParseTerm`, `ParseFactr`).

We are going to see an example of ParseInstr: `print 3;`.

Firstly we see the `ParseIntr` statement to parse this order:

```csharp 
else if ( this.tokens[ this.index ].Equals( "print" ) ) { 
    this.index++; 
    PrintStmt print_ = new PrintStmt(); 
    print_.Expression = this.ParseExpr(); 
    result = print_; 
}
```

The `print` token is discarded and we call `ParseExpr` to obtain the expression needed after the token `print` (grammar rules). We follow the call-list until `ParseFactr`. The implementation of `ParseFactr` is:

```csharp
private FactorStmt ParseFctr() { 
    if( this.index == this.tokens.Count ) { 
        throw new System.Exception( "Expected statement, got EOF." ); 
    }

    if( this.tokens[ this.index ] is Text.StringBuilder ) {
        string strValue = ( (Text.StringBuilder) this.tokens[ this.index++ ] ).ToString();
        StringLt stringLiteral = new StringLt();
        stringLiteral.Value = strValue;
        return stringLiteral;
    }
    else if( this.tokens[ this.index ] is int ) {
        int intValue = (int) this.tokens[ this.index++ ];
        IntLt intLiteral = new IntLt();
        intLiteral.Value = intValue;
        return intLiteral;
    }
    else if( this.tokens[ this.index ] is float ) {
        float floatValue = (float) this.tokens[ this.index++ ];
        FloatLt floatLiteral = new FloatLt();
        floatLiteral.Value = floatValue;
        return floatLiteral;
    }
    else if( this.tokens[ this.index ] is string ) {
        string identifier = (string) this.tokens[ this.index++ ];
        Variable variable = new Variable();
        variable.Identifier = identifier;
        return variable;
    }
    else if( this.tokens[ this.index ].Equals( Scanner.tokens.LBracket ) ) {
        this.index++; // LBracket
        BracketExpr expr = new BracketExpr();
        expr.Expression = this.ParseExpr();
        if ( this.tokens[ this.index ].Equals( Scanner.tokens.RBracket ) ) {
            this.index++; // RBracket
            return expr;
        }
        else {
            throw new System.Exception( "Expected "right bracket" after "left bracket" and "expression"." );
        }
    }
    else {
        throw new System.Exception( "Expected "string literal", "int literal", "float literal", or variable." );
    }
}
```

Here, at the lowest level of the parsing function we define with is the operand of each operation. In this case we return a int literal.

Consecutive instructions are allows using the "Squence token", implemented in `ParseInstr` as:

```csharp
if ( this.index < this.tokens.Count && this.tokens[ this.index ].Equals( Scanner.tokens.Semicolon ) ) {

    this.index++;

    if ( this.index < this.tokens.Count && !this.tokens[ this.index ].Equals( "else" ) &&
            !this.tokens[ this.index ].Equals( "endif" ) && !this.tokens[ this.index ].Equals( "endwhile" ) ) {
        Sequence sqc = new Sequence();
        sqc.First = result;
        sqc.Second = this.ParseInstr();
        result = sqc;
    }
    
    } else if ( !this.tokens[ this.index ].Equals( Scanner.tokens.Semicolon ) ) { 
        throw new System.Exception("Expected "semicolon" after token " + this.tokens[ this.index-1 ].ToString()); 
    } 
```

This sequence node contains two pointers to `InstrStmt` nodes and forms the basis of the syntax-tree structure.
