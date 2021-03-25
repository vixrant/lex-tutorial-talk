Converted from doc.tex.

This is a tutorial to Lex, with quick introduction to Yacc, presented to my juniors for their
Systems Programming and Compiler Construction course. Presented on 25/Mar/2021.

# Lexical Analysis

### Lexical Analysis

![Lexer operation](./imgs/lexer.png)

### Tokens

<token, value>

-   token = unique integer value, e.g. ID = 1, INT = 2

-   value (optional) = data related to token, e.g. "x", 5

Hence, we have <ID, "x">, <INT, 5>

### Lexer as DFA

![Lexical Analysis as a DFA](./imgs/dfa.jpg)

**Problem:** Writing so much logic is very tedious and unmaintainable in
code!

# Lex/ Flex

### Lex/ Flex

-   Computer program that generates lexical analyzers (also known as
    "scanners\"\" or "lexers")

-   Flex (fast lexical analyzer generator) is a free and open-source
    software alternative to lex

It is not a framework or a library, it writes the code for you

### Why Lex

-   Write rules, not code

-   Regular Expression "Patterns" for DFA building

-   Streaming lexer, does not load entire files all at once

-   Hence, it is extremely fast

-   Written for C orignally but can be used for C++

-   Reimplemented for other languages like Rust, Go, Python\...

## Example 1

    %%

    [0-9]           printf("Digit: %s \n", yytext);
    \n              printf("New line \n");
    .               printf("Any: %s \n", yytext);

These 4 lines generate 1734 lines of C code!

**%% Sections:** The %% is important because it marks the start of Rules
sections

![Regex pattern primitives](imgs/regex.png)

**yytext:**yytext is a global variable that stores the text of the lexeme
matched by regex.

### Using flex

Generate code using:

            flex <filename>
            gcc -lfl lex.yy.c -o <outputname>

Run interactive mode:

            ./<outputname>

Pass in a file's text:

            cat <file> | ./<outputname>

## Example 2

    DIGIT    [0-9]
    ID       [a-zA-Z][a-zA-Z0-9]*

    %%

    [\t ]           { /* Perform no action */ }
    {DIGIT}+        printf("Digit: %s \n", yytext);
    {ID}            printf("Identifier: %s \n", yytext);
    \n              printf("New line \n");

**%% Sections:** The section above %% is Declarations section with C code,
declarations (DIGIT, ID), and other configurations.

## Example 3

``` {.c basicstyle="\\tiny" language="c"}
... declarations

%%
... rules

%%

int main(argc, argv)
int argc;
char **argv;
{
    ++argv, --argc;
    if ( argc > 0 )
            yyin = fopen( argv[0], "r" );
    else
            yyin = stdin;

    yylex();
}
```

**%% Sections:** An optional user code section can be added after the rules.
This section is copied verbatim, i.e. directly into the generated code.

**yyin:** File to be processed. Set it to a readable file pointer. Default is
stdin i.e. command line input.

**yylex:** Continuously lexically analyse a file. If you return something, it
will pause execution and start again if you call it again. You can
change the return type by modifying YYDECL macro (advanced).

# Summary

## Code structure

``` {basicstyle="\\tiny"}
[
%{
... user code for header files and other config
%}
]

... declarations, if any

%%
... rules in tabular form, regex and action

[
%%
... user code for anything after the rules, like main
]
```

Where \[ block \] means optional

**Standard structure:** This structure is used in all Lex implementations.
Yacc and its implementations also uses same structure!

## Variables

![Predefined global variables](imgs/vars.png)

# More examples

## Assembly-like language

### Example 4

**Assembly-like calculator**

``` {basicstyle="\\small"}
ADD 5;
PRINT;

* 4;
PRINT;
/ -2;
ADD 7;
PRINT;

sub 13;
print;

clear;
print;
EXIT;
```

## Yacc/ Bison

### Yacc - Parser Generator

![Lexer to Parser pipeline](imgs/parsingpipeline.png)

### Integrating Yacc/Bison with Lex/Flex

![Steps to compile Lex and Yacc/ Flex and
Bison](imgs/yacc.png)

**yylval:** yylval, defined in parser.tab.c, is used to store data related to
tokens (value from <token, value>). It's datatype is defined using
YYSTYPE macro and is usually defined by Yacc/ Bison.

### Example 5

**Calculator with infix syntax**

        1 + 2
        3.0 / 2
        2 * (3.1427 / 3)
        1.1 + 2 - 3 * 4 / 5.

is based on

        E -> E + E | E - E | T
        T -> T * F | T / F | F
        F -> ( E ) | num | id

Code is generated using Yacc/ Bison.

# D.I.Y.

-   CSV parser using Flex

-   HTML/ XML parser using Flex

-   JSON/ YAML/ TOML/ CFG/ INI parser using Flex or Flex+Bison

-   Assembly-like stack machine using Flex or Flex+Bison

-   Pascal/ TCL/ C language parser using Flex+Bison

-   Use C++ and multiple files, explore the option flags

# References

[1] Tom Niemann. Lex yacc tutorial. URL https://cse.iitkgp.ac.in/~bivasm/notes/LexAndYaccTutorial.pdf.

[2] John Millaway Vern Paxson, Will Estes. Lexical analysis with flex. 2012. URL https://www.iith.ac.in/~ramakrishna/Compilers-Aug14/doc/flex.pdf.

[3] Keith Schwarz Julie Zelenski. flex in a nutshell. 2012. URL https://web.stanford.edu/class/archive/cs/cs143/cs143.1128/handouts/050%20Flex%20In%20A%20Nutshell.pdf.

[4] Lan Gao. Flex tutorial. URL http://alumni.cs.ucr.edu/~lgao/teaching/flex.html.

[5] Flex. The flex manual page. 2021. URL http://dinosaur.compilertools.net/flex/manpage.html.
