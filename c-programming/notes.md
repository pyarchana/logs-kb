# C Programming

Notes on C, written around tracing code by hand and predicting what it prints.

Topics covered so far: basics, tokens, data types, operators, branching, loops, 1D arrays.

## Contents

1. [Basics of a C Program](#part-1-basics-of-a-c-program)
2. [C Tokens](#part-2-c-tokens)
3. [Data Types](#part-3-data-types)
4. [Operators](#part-4-operators)
5. [Branching Statements](#part-5-branching-statements)
6. [Loops](#part-6-loops)
7. [Arrays](#part-7-arrays)
8. [Mistakes I Made](#mistakes-i-made)

---

## Part 1: Basics of a C Program

### Structure of a C program

```c
#include <stdio.h>      // preprocessor directive: pulls in printf, scanf

int main() {            // execution always starts at main
    printf("Hello");    // every statement ends with ;
    return 0;           // 0 tells the OS the program ended normally
}
```

### How a C program runs

```
Source code (.c) → Preprocessor → Compiler → Assembler → Linker → Executable
```

- **Preprocessor** - handles lines starting with `#` (`#include`, `#define`). Pure text replacement, happens before compiling.
- **Compiler** - converts C to assembly, catches syntax errors.
- **Assembler** - converts assembly to machine code (object file `.o`).
- **Linker** - joins object files and library code (like `printf`) into one executable.

---

## Part 2: C Tokens

A token is the smallest meaningful unit in a program. There are 6 types:

| Token | Examples |
|---|---|
| Keywords | `int`, `if`, `while`, `return`, `sizeof` |
| Identifiers | `count`, `_temp`, `sum1` |
| Constants | `10`, `3.14`, `'a'`, `012`, `0x1A` |
| String literals | `"hello"` |
| Operators | `+`, `==`, `&&`, `++` |
| Special symbols | `{ } ( ) [ ] ; ,` |

### Identifier rules

- Can contain letters, digits, underscore
- Cannot start with a digit (`1sum` is invalid)
- Cannot be a keyword (`int int;` is invalid)
- Case-sensitive (`Sum` and `sum` are different)

### Integer constants in different bases

This catches a lot of people. A leading `0` means **octal**.

```c
int a = 12;     // decimal 12
int b = 012;    // octal → 1*8 + 2 = 10
int c = 0x12;   // hex   → 1*16 + 2 = 18
printf("%d %d %d", a, b, c);   // 12 10 18
```

`int d = 09;` is a compile error, because 9 is not an octal digit.

### Character constants

A `char` is stored as a small integer (its ASCII value).

```c
char ch = 'A';
printf("%c %d", ch, ch);   // A 65
printf("%c", 'A' + 2);     // C
```

Useful ASCII values: `'0'` = 48, `'A'` = 65, `'a'` = 97.

---

## Part 3: Data Types

| Type | Typical size | Range (signed) |
|---|---|---|
| `char` | 1 byte | -128 to 127 |
| `short` | 2 bytes | -32768 to 32767 |
| `int` | 4 bytes | about -2.1 billion to 2.1 billion |
| `float` | 4 bytes | ~6-7 decimal digits of precision |
| `double` | 8 bytes | ~15-16 decimal digits of precision |

Sizes depend on the machine. The only guarantee is `sizeof(char)` is 1, and `char <= short <= int <= long`.

### Signed vs unsigned

An n-bit signed type holds -2^(n-1) to 2^(n-1) - 1. An unsigned one holds 0 to 2^n - 1.

For `char` (8 bits): signed is -128 to 127, unsigned is 0 to 255.

### Overflow (wraparound)

When a value goes past the range, it wraps around.

```c
unsigned char u = 255;
u = u + 1;
printf("%d", u);    // 0

unsigned int x = 0;
x = x - 1;
printf("%u", x);    // 4294967295 (huge value, not -1)

char c = 130;       // on a typical signed char
printf("%d", c);    // -126   (130 - 256)
```

Trick for signed char: if the value is above 127, subtract 256.

### Format specifiers

| Specifier | Prints |
|---|---|
| `%d` | signed int |
| `%u` | unsigned int |
| `%c` | character |
| `%f` | float / double |
| `%o` | octal |
| `%x` | hex |
| `%s` | string |

---

## Part 4: Operators

### Precedence and associativity (high to low)

| Level | Operators | Associativity |
|---|---|---|
| 1 | `()` `[]` `->` `.` postfix `++ --` | left to right |
| 2 | prefix `++ --`, unary `+ - ! ~`, `sizeof`, cast | right to left |
| 3 | `* / %` | left to right |
| 4 | `+ -` | left to right |
| 5 | `<< >>` | left to right |
| 6 | `< <= > >=` | left to right |
| 7 | `== !=` | left to right |
| 8 | `&` | left to right |
| 9 | `^` | left to right |
| 10 | `\|` | left to right |
| 11 | `&&` | left to right |
| 12 | `\|\|` | left to right |
| 13 | `?:` | right to left |
| 14 | `= += -=` etc. | right to left |
| 15 | `,` | left to right |

Short memory line: **unary > arithmetic > shift > relational > equality > bitwise > logical > ternary > assignment > comma**.

### Integer division and modulus

When both operands are integers, the result is an integer. The decimal part is thrown away (truncates **toward zero**).

```c
printf("%d", 7 / 2);     //  3
printf("%d", -7 / 2);    // -3  (not -4)
printf("%d", 7 % 2);     //  1
printf("%d", -7 % 2);    // -1
printf("%d", 7 % -2);    //  1
```

Rule for `%`: the result takes the **sign of the left operand** (the dividend).

Check: `a == (a / b) * b + (a % b)` is always true. For -7 and 2: (-3)(2) + (-1) = -7.

`%` only works on integers. `5.0 % 2` is a compile error.

### Implicit type conversion

In a mixed expression, the smaller type is converted to the bigger one.

```c
printf("%d", 5 / 2);        // 2    (int / int)
printf("%f", 5 / 2.0);      // 2.5  (int / double → double)
printf("%f", (float)5 / 2); // 2.5  (cast first, then divide)
printf("%f", (float)(5 / 2)); // 2.0 (divide first, then cast)
```

**Signed vs unsigned trap:** when `int` meets `unsigned int`, the int is converted to unsigned.

```c
if (-1 > 1u)
    printf("yes");   // prints yes
```

-1 becomes 4294967295 as unsigned, which is greater than 1.

### Increment and decrement

- `++i` (pre) - increment first, then use the new value
- `i++` (post) - use the current value, then increment

```c
int i = 5;
int a = i++;   // a = 5, i = 6
int b = ++i;   // i = 7, b = 7
```

Expressions like `i = i++ + ++i` are **undefined behavior** (modifying the same variable twice without a sequence point). The output can differ between compilers, so never rely on them.

### Relational and logical operators return 1 or 0

```c
printf("%d", 5 > 3);     // 1
printf("%d", 5 == 3);    // 0
printf("%d", !5);        // 0
printf("%d", !0);        // 1
printf("%d", 3 < 2 < 1); // 1   (3 < 2 is 0, then 0 < 1 is 1)
```

### Short-circuit evaluation

With `&&`, if the left side is false, the right side is **never evaluated**.
With `||`, if the left side is true, the right side is **never evaluated**.

```c
int a = 0, b = 5;
if (a && b++) ;
printf("%d", b);   // 5  (b++ never ran)

int x = 1, y = 5;
if (x || y++) ;
printf("%d", y);   // 5  (y++ never ran)
```

**How to solve these:** evaluate the left side first. Decide if the right side even runs. Only then apply any `++` or `--` on the right.

### Bitwise operators

| Operator | Meaning | Example (a = 5 = 0101, b = 3 = 0011) |
|---|---|---|
| `&` | AND | `a & b` = 0001 = 1 |
| `\|` | OR | `a \| b` = 0111 = 7 |
| `^` | XOR | `a ^ b` = 0110 = 6 |
| `~` | NOT | `~a` = -6 (for signed int, `~x = -x - 1`) |
| `<<` | left shift | `a << 1` = 10 (multiply by 2) |
| `>>` | right shift | `a >> 1` = 2 (divide by 2) |

`&` vs `&&`: `&` works bit by bit and always evaluates both sides. `&&` gives 1 or 0 and short-circuits.

```c
printf("%d", 2 & 1);    // 0  (10 & 01)
printf("%d", 2 && 1);   // 1  (both non-zero)
```

### Ternary operator

```c
int max = (a > b) ? a : b;
```

Nested ternary groups right to left:

```c
x = a > b ? a : b > c ? b : c;
// same as: a > b ? a : (b > c ? b : c)
```

### Comma operator

Evaluates left to right, and the value of the whole expression is the **last** one.

```c
int x = (1, 2, 3);   // x = 3
```

Without brackets, `=` has higher precedence than `,`:

```c
int x;
x = 1, 2, 3;         // x = 1
```

### sizeof

`sizeof` is evaluated at **compile time**. The expression inside it is not executed.

```c
int i = 5;
printf("%zu", sizeof(i++));   // 4
printf("%d", i);              // 5  (i++ never ran)
```

### Assignment inside if

```c
int x = 5;
if (x = 0)            // assigns 0, then checks 0 → false
    printf("A");
else
    printf("B");      // prints B, and x is now 0
```

---

## Part 5: Branching Statements

### if, if-else, else-if ladder

```c
if (marks >= 90)
    printf("A");
else if (marks >= 75)
    printf("B");
else
    printf("C");
```

**Any non-zero value is true**, including negatives. Only 0 is false.

```c
if (-1) printf("yes");   // prints yes
```

### if without braces

Without `{ }`, the `if` controls **only the next statement**.

```c
if (0)
    printf("A");
    printf("B");     // always runs, indentation means nothing
// output: B
```

### Dangling else

An `else` always pairs with the **nearest unmatched `if`**, no matter how it is indented.

```c
int a = 0, b = 1;
if (a)
    if (b)
        printf("X");
else
    printf("Y");
// prints nothing
```

The `else` belongs to `if (b)`, not `if (a)`. Since `a` is 0, the whole inner part is skipped.

### switch

```c
switch (expression) {
    case 1: ...; break;
    case 2: ...; break;
    default: ...;
}
```

Rules:
- The expression and case labels must be **integer type** (int, char). No float, no strings.
- Case labels must be **constants**. `case x:` with a variable is an error.
- No duplicate case labels.
- `default` is optional and can be placed anywhere.

**Fall-through:** without `break`, execution continues into every case below the matching one.

```c
int x = 2;
switch (x) {
    case 1: printf("A");
    case 2: printf("B");
    case 3: printf("C");
    default: printf("D");
}
// output: BCD
```

Starts at the matching case (2), then falls through 3 and default.

`default` in the middle:

```c
int x = 5;
switch (x) {
    case 1: printf("A");
    default: printf("D");
    case 2: printf("B");
}
// output: DB
```

No case matches, so it jumps to `default`, then falls through into `case 2`.

---

## Part 6: Loops

### while

Checks the condition **first**, then runs the body. May run 0 times.

```c
int i = 0;
while (i < 3) {
    printf("%d ", i);
    i++;
}
// 0 1 2
```

### do-while

Runs the body **first**, then checks the condition. Always runs **at least once**.

```c
int i = 10;
do {
    printf("%d ", i);   // runs once: 10
    i++;
} while (i < 5);        // 11 < 5 is false, stop
// output: 10
```

With a plain `while (i < 5)` here, nothing would print.

Note the semicolon after `while (...)` in do-while. It is required.

### for

```c
for (init; condition; update) {
    body;
}
```

Order of execution:
1. `init` runs once
2. check `condition`, if false stop
3. run `body`
4. run `update`
5. go back to step 2

All three parts are optional. `for (;;)` is an infinite loop.

### Counting iterations

`for (i = a; i < b; i++)` runs **b - a** times.
`for (i = a; i <= b; i++)` runs **b - a + 1** times.

Watch the final failing check with post-increment:

```c
int i = 0, count = 0;
while (i++ < 3)
    count++;
printf("%d %d", i, count);   // 4 3
```

Trace:

| Check | Compared value | Result | i after | count |
|---|---|---|---|---|
| 1 | 0 < 3 | true | 1 | 1 |
| 2 | 1 < 3 | true | 2 | 2 |
| 3 | 2 < 3 | true | 3 | 3 |
| 4 | 3 < 3 | false | 4 | 3 |

`i` is incremented even on the check that fails.

### Stray semicolon after a loop

A semicolon alone is an empty statement. Placed right after the loop header, it **becomes the loop body**.

```c
int i;
for (i = 0; i < 5; i++);    // loop body is just ";"
printf("%d", i);            // runs once, after the loop
// output: 5
```

Compare with the normal version, which prints 0 1 2 3 4:

```c
for (i = 0; i < 5; i++)
    printf("%d", i);
```

Same trap with while. This one is an infinite loop because `i++` is outside the loop:

```c
int i = 0;
while (i < 5);
    i++;
```

### break and continue

- `break` - exits the loop immediately. In nested loops, it exits **only the innermost** loop.
- `continue` - skips the rest of the body and goes to the next iteration.

In a `for` loop, `continue` still runs the **update** part:

```c
for (int i = 0; i < 5; i++) {
    if (i == 2) continue;
    printf("%d ", i);
}
// 0 1 3 4
```

In a `while` loop, `continue` jumps straight to the condition. If the increment is after the `continue`, it gets skipped:

```c
int i = 0;
while (i < 5) {
    if (i == 2) continue;   // i stays 2 forever
    printf("%d ", i);
    i++;
}
// 0 1 then infinite loop
```

### Nested loops

Total inner iterations = outer iterations × inner iterations (when the inner count is fixed).

```c
int count = 0;
for (int i = 0; i < 3; i++)
    for (int j = 0; j < 4; j++)
        count++;
// count = 12
```

When the inner loop depends on the outer variable, add them up:

```c
int count = 0;
for (int i = 1; i <= 4; i++)
    for (int j = 1; j <= i; j++)
        count++;
// count = 1 + 2 + 3 + 4 = 10
```

In general, `n(n+1)/2`.

### Loop counter changed inside the body

```c
for (int i = 0; i < 10; i++) {
    printf("%d ", i);
    i += 2;
}
// 0 3 6 9   (i goes up by 3 each round: +2 in body, +1 in update)
```

---

## Part 7: Arrays

An array is a group of elements of the **same type**, stored **next to each other** in memory, accessed by an index.

### Declaring an array

```c
int a[5];        // 5 ints: a[0] to a[4]
char name[20];   // 20 chars
float marks[3];
```

- The size must be a positive integer. `int a[0];` and `int a[-3];` are invalid.
- Indexing starts at **0**, so the last element is `a[n - 1]`.
- Since C99, a local array's size can be a variable (`int n = 5; int a[n];`). This is called a VLA, and it **cannot** be initialized with `{ }`.

### Initializing

```c
int a[5] = {1, 2, 3, 4, 5};   // all 5 given
int b[5] = {1, 2, 3};         // 1 2 3 0 0  (rest become 0)
int c[5] = {0};               // 0 0 0 0 0  (common way to zero an array)
int d[]  = {1, 2, 3};         // size is taken from the list → 3
int e[5] = {[2] = 7};         // 0 0 7 0 0  (C99 designated initializer)
int f[2] = {1, 2, 3};         // invalid: more values than size
```

Rule: if **at least one** value is given, every element not given becomes 0.

**No initializer at all:**
- Local array (inside a function) → contains **garbage** values
- Global or `static` array → all **0**

```c
int g[3];              // global → 0 0 0

int main() {
    int h[3];          // local → garbage
}
```

### Memory layout

Elements sit one after another with no gaps. If the array starts at address `base`:

```
address of a[i] = base + i × sizeof(element)
```

Example: `int a[10]` starts at 1000, `int` is 4 bytes.

| Element | Address |
|---|---|
| `a[0]` | 1000 |
| `a[1]` | 1004 |
| `a[3]` | 1000 + 3 × 4 = 1012 |
| `a[9]` | 1036 |

### sizeof and number of elements

```c
int a[] = {10, 20, 30, 40};
printf("%zu", sizeof(a));                  // 16  (4 elements × 4 bytes)
printf("%zu", sizeof(a) / sizeof(a[0]));   // 4   (number of elements)
```

`sizeof(a) / sizeof(a[0])` is the standard way to get the length. It only works where the array itself is visible, not on an array passed into a function (covered with functions).

### No bounds checking

C does **not** check whether an index is inside the array.

```c
int a[5];
a[5] = 10;    // compiles, but writes outside the array
printf("%d", a[7]);   // compiles, reads outside the array
```

This is **undefined behavior**: it may print garbage, crash, or silently overwrite another variable. The compiler won't stop you.

The classic cause is an off-by-one loop:

```c
for (i = 0; i <= 5; i++)   // wrong: i = 5 is out of bounds
    a[i] = 0;

for (i = 0; i < 5; i++)    // correct
    a[i] = 0;
```

### Array name and indexing

The array name, used in an expression, gives the **address of the first element**.

```c
int a[] = {1, 2, 3, 4, 5};
// a  is the same address as  &a[0]
```

`a[i]` is just shorthand for `*(a + i)`: start at the first element and move `i` elements forward. Since addition can be swapped, all four of these are the same:

```c
a[2]      // 3
*(a + 2)  // 3
*(2 + a)  // 3
2[a]      // 3   (looks wrong, but valid C)
```

`*` and addresses are covered properly with pointers. For now, remember that `i[a]` works and means `a[i]`.

### What you cannot do with arrays

```c
int a[3] = {1, 2, 3}, b[3];

b = a;        // error: arrays can't be assigned
a++;          // error: the array name can't be changed
if (a == b)   // compiles, but compares addresses, not contents → always false here
```

To copy or compare, use a loop element by element:

```c
for (i = 0; i < 3; i++)
    b[i] = a[i];
```

### Common operations

**Sum of elements**

```c
int sum = 0;
for (i = 0; i < n; i++)
    sum += a[i];
```

**Largest element**

```c
int max = a[0];            // start with the first element, not 0
for (i = 1; i < n; i++)
    if (a[i] > max)
        max = a[i];
```

Starting `max` at 0 breaks when all elements are negative.

**Reverse in place**

```c
for (i = 0; i < n / 2; i++) {
    int t = a[i];
    a[i] = a[n - 1 - i];
    a[n - 1 - i] = t;
}
// {1, 2, 3, 4, 5} → {5, 4, 3, 2, 1}
```

Going up to `n / 2` matters. Looping all the way to `n` swaps everything twice and gives back the original array.

**Linear search**

```c
int pos = -1;
for (i = 0; i < n; i++) {
    if (a[i] == key) {
        pos = i;
        break;
    }
}
// pos is the index of key, or -1 if not found
```

---

## Mistakes I Made

Things I got wrong in practice, so I don't repeat them.

- **Stray semicolon:** `for (i = 0; i < 5; i++); printf("%d", i);` prints `5`, not `01234`. The semicolon is the loop body.
- **do-while:** runs once even if the condition is false from the start.
- **Short-circuit:** in `a && b++` with `a = 0`, `b++` never runs.
