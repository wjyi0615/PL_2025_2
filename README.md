# PL_2025_2
Principles of Programming Languages Autumn, 2025
## Project Overview

This program consists of a **lexical analyzer** and a **parser**. It reads sentences written in the input file, analyzes them, and then detects variable definitions, expression evaluation, syntax errors, etc., and prints the results.
In addition, C language keywords (reserved words) are preprocessed in advance so that they are distinguished from identifiers.

---

## 1. Main File Structure

| File Name                  | Role                                                                                                              |
| -------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| `main.cpp`                 | Program entry point. Receives the input file and performs lexical analysis and parsing.                           |
| `LexicalAnalyzer.h / .cpp` | Lexical analyzer. Reads the input character by character and generates tokens.                                    |
| `Parser.h / .cpp`          | Parser. Builds a parse tree according to grammar rules based on the generated tokens and performs error handling. |

---

## 2. Lexical Analyzer

### LexicalAnalyzer.cpp

| Function Name                                                                                                                                               | Description                                                                                                        |
| ----------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------ |
| `lookup(char ch)`                                                                                                                                           | Recognizes operators and delimiters (`+`, `-`, `*`, `/`, `;`, `(`, `)`, `:=`) and returns the corresponding token. |
| `addChar()`                                                                                                                                                 | Adds the current character (`nextChar`) to the lexeme buffer.                                                      |
| `getChar()`                                                                                                                                                 | Reads the next character from the input file and determines the character class (`LETTER`, `DIGIT`, `UNKNOWN`).    |
| `getNonBlank()`                                                                                                                                             | Skips whitespace characters (space, tab, newline, etc.).                                                           |
| `lexical()`                                                                                                                                                 | Core function of the overall lexical analysis.                                                                     |
| Recognizes identifiers, keywords, numeric literals, operators, parentheses, semicolons, EOF, etc. according to the character class and returns `nextToken`. |                                                                                                                    |

### Keyword Preprocessing

* The program defines in advance a list of C language reserved words (`C_KEYWORDS`).
* During lexical analysis, if a string that was first recognized as an identifier (`IDENT`)
  is included in this set, it is classified as a `KEYWORD` token.
* This allows the program to distinguish C reserved words from user-defined variable names,
  and the parser reports an error when a keyword appears in a position where an identifier is required.

**Example**
Input: `int := 10;`
Output: `(ERROR) "C keyword (int) cannot be used as an identifier."`

### LexicalAnalyzer.h

* Defines character classes (`CharClass`) and token codes (`TokenCode`) as `enum`.
* Declares global variables (`lexeme`, `nextChar`, `nextToken`, `in_fp`) and functions.

---

## 3. Parser

| Function Name                        | Description                                                                                                                              |
| ------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------- |
| `program()`                          | Executes the start rule `<program> → <statements>`. Also prints the symbol table.                                                        |
| `statements()`                       | Parses multiple statements and separates them by semicolons (`;`). Handles duplicate semicolons and performs syntax error recovery.      |
| `statement()`                        | Parses a single statement. Consists of an identifier, assignment operator (`:=`), and expression `<expression>`.                         |
| `expression()`, `term()`, `factor()` | Recursively implement the grammar rules of expressions, handling `+`, `-`, `*`, `/` operations.                                          |
| `term_tail()`, `factor_tail()`       | Handle the tail part of polynomials or product expressions. Detect errors such as duplicate operators or mixing incompatible operators.  |
| `freeTree()`                         | Recursively frees the memory of the parse tree.                                                                                          |
| `resetCounts()`, `printCounts()`     | Count and print the number of identifiers (ID), constants (CONST), and operators (OP).                                                   |
| Error handling                       | Prints detailed error messages for undefined variables, invalid tokens, missing parentheses, duplicate operators, division by zero, etc. |

---

## 4. Main Function

1. Receives the input file as a command-line argument (`argv[1]`).
2. Prints an error message if the file fails to open.
3. Reads the first character (`getChar()`) and runs lexical analysis (`lexical()`).
4. Performs the overall parsing via the `program()` function.
5. Frees the parse tree (`freeTree()`) and closes the file.

---

## 5. Program Flow Summary

Open input file
↓
Lexical Analyzer (`LexicalAnalyzer`)
↓
├─ Keyword preprocessing (recognize C reserved words)
├─ Convert each character into a token
↓
Parser (`Parser`)
└─ Analyze tokens according to grammar rules
↓
Print error and result messages
↓
Display symbol table and counts of ID / CONST / OP

---

## 6. Main Features and Improvements

* Added preprocessing of C language keywords and separation from identifiers.
* Recognizes both integer and floating-point literals and supports error recovery.
* Handles detailed errors such as duplicate semicolons, invalid assignment statements, and unclosed parentheses.
* Structures the parse tree and prints the results in a visual form.
* Provides a summary of symbol table results in the form of `Result ==> ...`.
