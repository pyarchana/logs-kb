# C Programming - Practice

Output-prediction questions. Trace each one by hand before opening the answer.

---

## Q1: Short-circuit

```c
int a = 0, b = 5;
if (a && b++) ;
printf("%d", b);
```

<details>
<summary>Answer</summary>

**5**

`a` is 0, so `&&` is already false and `b++` never runs.
</details>

---

## Q2: switch fall-through

```c
int x = 2;
switch (x) {
    case 1: printf("A");
    case 2: printf("B");
    case 3: printf("C");
    default: printf("D");
}
```

<details>
<summary>Answer</summary>

**BCD**

No `break`, so it starts at case 2 and falls through everything below it.
</details>

---

## Q3: Stray semicolon

```c
int i;
for (i = 0; i < 5; i++);
printf("%d", i);
```

<details>
<summary>Answer</summary>

**5**

The `;` after the `for` is the loop body. `printf` runs once, after the loop ends.
</details>

---

## Q4: do-while

```c
int i = 10;
do {
    printf("%d ", i);
    i++;
} while (i < 5);
```

<details>
<summary>Answer</summary>

**10**

do-while runs the body before checking, so it always runs at least once.
</details>

---

## Q5: Post-increment in a condition

```c
int i = 0, count = 0;
while (i++ < 3)
    count++;
printf("%d %d", i, count);
```

<details>
<summary>Answer</summary>

**4 3**

The body runs 3 times. On the 4th check, `3 < 3` is false, but `i` is still incremented to 4.
</details>

---

## Q6: Negative division

```c
printf("%d %d", -7 / 2, -7 % 2);
```

<details>
<summary>Answer</summary>

**-3 -1**

Division truncates toward zero. `%` takes the sign of the left operand.
</details>

---

## Q7: Dangling else

```c
int a = 0, b = 1;
if (a)
    if (b)
        printf("X");
else
    printf("Y");
```

<details>
<summary>Answer</summary>

**Nothing is printed**

The `else` pairs with the nearest `if`, which is `if (b)`. Since `a` is 0, the whole inner part is skipped.
</details>

---

## Q8: sizeof

```c
int i = 5;
int s = sizeof(i++);
printf("%d", i);
```

<details>
<summary>Answer</summary>

**5**

`sizeof` is worked out at compile time, so `i++` never runs.
</details>

---

## Q9: Signed vs unsigned

```c
if (-1 > 1u)
    printf("yes");
else
    printf("no");
```

<details>
<summary>Answer</summary>

**yes**

-1 is converted to unsigned, which makes it 4294967295.
</details>

---

## Q10: continue in a while loop

```c
int i = 0;
while (i < 5) {
    if (i == 2) continue;
    printf("%d ", i);
    i++;
}
```

<details>
<summary>Answer</summary>

**0 1, then an infinite loop**

When `i` is 2, `continue` skips `i++`, so `i` stays 2 forever.
</details>
---

## Q11: Partial initialization

```c
int a[5] = {1, 2};
printf("%d %d", a[1], a[4]);
```

<details>
<summary>Answer</summary>

**2 0**

Once some values are given, the rest are filled with 0.
</details>

---

## Q12: Number of elements

```c
int a[] = {10, 20, 30, 40};
printf("%zu %zu", sizeof(a), sizeof(a) / sizeof(a[0]));
```

<details>
<summary>Answer</summary>

**16 4** (with 4-byte int)

`sizeof(a)` is the total size in bytes. Dividing by one element's size gives the count.
</details>

---

## Q13: Swapped index

```c
int a[] = {1, 2, 3, 4, 5};
printf("%d", 2[a]);
```

<details>
<summary>Answer</summary>

**3**

`2[a]` is `*(2 + a)`, which is the same as `*(a + 2)`, which is `a[2]`.
</details>

---

## Q14: Designated initializers

```c
int a[5] = {[1] = 5, [3] = 9};
for (int i = 0; i < 5; i++)
    printf("%d ", a[i]);
```

<details>
<summary>Answer</summary>

**0 5 0 9 0**

Only positions 1 and 3 are set. Everything else becomes 0.
</details>

---

## Q15: Address calculation

`int a[10]` starts at address 2000, and `int` takes 4 bytes. What is the address of `a[6]`?

<details>
<summary>Answer</summary>

**2024**

2000 + 6 × 4 = 2024.
</details>

---

## Q16: Ternary inside a loop

```c
int a[] = {1, 2, 3, 4, 5}, s = 0;
for (int i = 0; i < 5; i++)
    s += a[i] % 2 ? a[i] : 0;
printf("%d", s);
```

<details>
<summary>Answer</summary>

**9**

`?:` has higher precedence than `+=`, so this is `s += (a[i] % 2 ? a[i] : 0)`. Only the odd values are added: 1 + 3 + 5 = 9.
</details>
