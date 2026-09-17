# Assignment 2: Recursive Descent Parser, CS 3210 Fall Section 002

## Your name

## Your student Number

## Description

Build a recursive descent parser in Java that uses the lexical analyzer from Assignment 1. Your parser will read tokens from your `Lexer` class via its `getNextToken()` method and generate a complete, compilable C program as output.

This assignment demonstrates the translation phase of a compiler: taking tokens and building a parse tree, then generating target code (C) from that tree.

## Requirements

### Input
- Your parser reads from stdin (via `System.in`)
- Input is a program written in the `.see` language (defined in Assignment 1)
- You must use your `Lexer` class and call its `getNextToken()` method

### Output
- Generate a complete, valid C program to stdout
- The C program must be compilable with `gcc`
- The generated C program must produce identical output to what the `.see` program describes

### Implementation

Create a single Java file named `Parser.java` that:
1. Instantiates your `Lexer` class
1. Implements a recursive descent parser for the grammar below
1. Generates C code
1. Outputs the complete C program to stdout

**Important:** Keep your `Lexer.java` from Assignment 1. The parser should be a separate class that uses your lexer.

## Grammar

Use the same grammar from Assignment 1:

```bnf
<program> ::= <statement_list>

<statement_list> ::= <statement>
                   | <statement> <statement_list>

<statement> ::= <assignment>
              | <if_statement>
              | <while_statement>
              | <block>
              | <print_statement>

<assignment> ::= <identifier> "=" <expression> ";"

<if_statement> ::= "if" "(" <expression> ")" <statement>
                 | "if" "(" <expression> ")" <statement> "else" <statement>

<while_statement> ::= "while" "(" <expression> ")" <statement>

<block> ::= "{" <statement_list> "}"
          | "{" "}"

<print_statement> ::= "print" "(" <expression> ")" ";"

<expression> ::= <logical_or>

<logical_or> ::= <logical_and>
               | <logical_or> "||" <logical_and>

<logical_and> ::= <equality>
                | <logical_and> "&&" <equality>

<equality> ::= <relational>
             | <equality> "==" <relational>
             | <equality> "!=" <relational>

<relational> ::= <additive>
               | <relational> "<" <additive>
               | <relational> ">" <additive>
               | <relational> "<=" <additive>
               | <relational> ">=" <additive>

<additive> ::= <primary>
             | <additive> "+" <primary>
             | <additive> "-" <primary>

<primary> ::= <integer>
            | <identifier>
            | "(" <expression> ")"

<identifier> ::= <letter> { <letter> | <digit> | "_" }

<integer> ::= <digit> { <digit> }
```

## C Code Generation Rules

### Header
Always include:
```c
#include <stdio.h>

int main() {
```

### Variables
- Declare variables on first use as `int <identifier>;` before any statement that uses them
- Or use a single `int` declaration at the start of `main()` for all identifiers

### Footer
End with:
```c
    return 0;
}
```

## How to Compile and Run

```bash
# Compile both Lexer and Parser
javac Lexer.java Parser.java

# Run the parser to generate C code
java Parser < test.see > test.c

# Compile the generated C program
gcc test.c -o test

# Run the generated program
./test
```

## Error Handling

Your parser should gracefully handle errors by:
- Checking token types before consuming them
- Providing meaningful error messages to stderr
- Exiting with a non-zero status on parse errors

Example error message:
```
Parse error: Expected RPAREN but got IDENTIFIER at line 3
```

## Grading Criteria

- **Correctness**: Parser correctly handles all grammar rules (40%)
- **Code generation**: Generated C code compiles and runs correctly (30%)
- **Code quality**: Clear structure, meaningful variable names, no dead code (20%)
- **Testing**: Demonstrates testing with multiple test cases (10%)
