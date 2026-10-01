# C Programming - Cheatsheet

Quick revision before a mock test. Full explanations in [notes.md](notes.md). Practice questions in [practice.md](practice.md).

**Sections:** [Tokens](#tokens) · [Data Types](#data-types) · [Operators](#operators) · [Branching](#branching) · [Loops](#loops) · [Arrays](#arrays-1d)

---

## Tokens

6 types: keywords, identifiers, constants, string literals, operators, special symbols.

Identifier: letters, digits, `_`. Can't start with a digit. Can't be a keyword. Case-sensitive.

| Constant | Value |
|---|---|
| `12` | 12 (decimal) |
| `012` | 10 (octal, leading 0) |
| `0x12` | 18 (hex) |
| `09` | compile error |
| `'A'` | 65 |
| `'a'` | 97 |
| `'0'` | 48 |

---

## Data Types

| Type | Size | Signed range |
|---|---|---|
| `char` | 1 | -128 to 127 |
| `short` | 2 | -32768 to 32767 |
| `int` | 4 | ~ ±2.1 billion |
| `float` | 4 | |
| `double` | 8 | |

- Unsigned char: 0 to 255
- Signed char above 127: subtract 256 (`char c = 130` → -126)
- Unsigned below 0 wraps to a huge value

---

## Operators

**Precedence:** unary > arithmetic > shift > relational > equality > bitwise (`& ^ |`) > `&&` > `||` > ternary > assignment > comma

**Right to left:** unary, ternary, assignment. Everything else left to right.

| Expression | Result | Why |
|---|---|---|
| `5 / 2` | 2 | int division |
| `5 / 2.0` | 2.5 | promoted to double |
| `-7 / 2` | -3 | truncates toward zero |
| `-7 % 2` | -1 | sign of left operand |
| `7 % -2` | 1 | sign of left operand |
| `-1 > 1u` | 1 (true) | -1 becomes huge unsigned |
| `3 < 2 < 1` | 1 | (3<2)=0, then 0<1 |
| `!5` | 0 | |
| `~5` | -6 | `~x = -x - 1` |
| `5 << 1` | 10 | × 2 |
| `5 >> 1` | 2 | ÷ 2 |
| `2 & 1` | 0 | bitwise |
| `2 && 1` | 1 | logical |
| `x = (1, 2, 3)` | x = 3 | comma gives last value |
| `x = 1, 2, 3` | x = 1 | `=` binds before `,` |
| `sizeof(i++)` | i unchanged | not evaluated |
| `if (x = 0)` | false | assignment, not comparison |

**Increment:** `i++` uses then increments. `++i` increments then uses.

**Short-circuit:**
- `0 && anything` → right side skipped
- `1 || anything` → right side skipped

**Undefined behavior:** `i = i++ + ++i`. Output depends on the compiler, avoid it.

---

## Branching

- Any non-zero (including negative) is true
- `if` without braces controls only the next statement
- `else` pairs with the nearest unmatched `if` (dangling else)

**switch:**
- Integer or char only (no float, no string)
- Case labels must be constants, no duplicates
- `default` is optional and can be anywhere
- No `break` → falls through to every case below

---

## Loops

| Loop | Checks condition | Minimum runs |
|---|---|---|
| `while` | before body | 0 |
| `for` | before body | 0 |
| `do-while` | after body | 1 |

**for order:** init (once) → condition → body → update → condition → ...

**Iteration count:**
- `i = a; i < b` → b - a times
- `i = a; i <= b` → b - a + 1 times
- Nested with `j <= i` → n(n+1)/2

**Traps:**
- `for (...);` → semicolon is the body
- `while (i++ < 3)` → i ends at 4, body runs 3 times
- `break` exits only the innermost loop
- `continue` in `for` still runs the update
- `continue` in `while` can skip the increment → infinite loop
- `for (;;)` and `while (1)` → infinite
- do-while needs `;` after `while (...)`

---

## Arrays (1D)

| Declaration | Contents |
|---|---|
| `int a[5] = {1, 2, 3};` | 1 2 3 0 0 |
| `int a[5] = {0};` | all 0 |
| `int a[] = {1, 2, 3};` | size 3 |
| `int a[5] = {[2] = 7};` | 0 0 7 0 0 |
| `int a[2] = {1, 2, 3};` | invalid |
| `int a[5];` (local) | garbage |
| `int a[5];` (global/static) | all 0 |

- Index runs 0 to n - 1
- `address of a[i] = base + i × sizeof(type)`
- `sizeof(a)` = total bytes; `sizeof(a) / sizeof(a[0])` = number of elements
- No bounds checking: `a[n]` compiles but is undefined behavior
- `a` = address of `a[0]`
- `a[i]` = `*(a + i)` = `*(i + a)` = `i[a]`
- `b = a;` and `a++;` → error
- `a == b` compares addresses, not contents
- Find max: start with `a[0]`, not 0
- Reverse: loop to `n / 2`, not `n`
