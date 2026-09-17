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
2. Implements a recursive descent parser for the grammar below
3. Builds an abstract syntax tree (AST) as you parse
4. Generates C code from the AST
5. Outputs the complete C program to stdout

**Important:** Keep your `Lexer.java` from Assignment 1 — do not modify it. The parser should be a separate class that uses your lexer.

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

### Operators
| `.see` Operator | C Equivalent |
|---|---|
| `+` | `+` |
| `-` | `-` |
| `<` | `<` |
| `>` | `>` |
| `<=` | `<=` |
| `>=` | `>=` |
| `==` | `==` |
| `!=` | `!=` |
| `&&` | `&&` |
| `||` | `||` |

### Statements
- `print(expr)` → `printf("%d\n", expr);`
- `if (cond) stmt` → `if (cond) stmt`
- `if (cond) stmt else stmt2` → `if (cond) stmt else stmt2`
- `while (cond) stmt` → `while (cond) stmt`
- Blocks `{ ... }` → C blocks `{ ... }`

### Footer
End with:
```c
    return 0;
}
```

## Example

### Input (test.see)
```
n = 10;
factorial = 1;
i = 1;

while (i <= n) {
    factorial = factorial + i;
    i = i + 1;
}

print(factorial);

power = 1;
while (power + power < n) {
    power = power + power;
}
print(power);

counter = n;
while (counter > 0) {
    print(counter);
    counter = counter - 1;
}

x = 15;
y = 20;

if (x < y) {
    print(1);
} else {
    print(0);
}
```

### Generated Output (test.c)
```c
#include <stdio.h>

int main() {
    int n, factorial, i, power, counter, x, y;
    n = 10;
    factorial = 1;
    i = 1;
    while (i <= n) {
        factorial = factorial + i;
        i = i + 1;
    }
    printf("%d\n", factorial);
    power = 1;
    while (power + power < n) {
        power = power + power;
    }
    printf("%d\n", power);
    counter = n;
    while (counter > 0) {
        printf("%d\n", counter);
        counter = counter - 1;
    }
    x = 15;
    y = 20;
    if (x < y) {
        printf("%d\n", 1);
    } else {
        printf("%d\n", 0);
    }
    return 0;
}
```

### When Compiled and Run (gcc test.c -o test && ./test)
```
56
8
10
9
8
7
6
5
4
3
2
1
1
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

## Testing Strategy

1. Start with simple assignments and print statements
2. Gradually add expressions with operators
3. Test control flow (if/else, while)
4. Test nested blocks
5. Test complex expressions with multiple operators
6. Verify generated C code compiles without warnings
7. Verify generated C code produces correct output

## Error Handling

Your parser should gracefully handle errors by:
- Checking token types before consuming them
- Providing meaningful error messages to stderr
- Exiting with a non-zero status on parse errors

Example error message:
```
Parse error: Expected RPAREN but got IDENTIFIER at line 3
```

## Hints

- Use a `current` variable to track the current token
- Use a `peek()` method to look at the current token without consuming it
- Use a `consume()` method that checks the current token type and advances
- Build an AST with classes like `Program`, `Statement`, `Expression`, etc.
- Use a visitor pattern or recursive method to generate C code from the AST
- Track variable names in a set as you encounter them for declarations

## Grading Criteria

- **Correctness**: Parser correctly handles all grammar rules (40%)
- **Code generation**: Generated C code compiles and runs correctly (30%)
- **Code quality**: Clear structure, meaningful variable names, no dead code (20%)
- **Testing**: Demonstrates testing with multiple test cases (10%)

## Submission

Submit:
1. `Parser.java` — your parser implementation
2. `Lexer.java` — copy from Assignment 1 (unmodified)
3. Any supporting AST classes you created
4. At least two `test*.see` files with expected output
5. Expected output files (`EXPECTED_*.txt`)
