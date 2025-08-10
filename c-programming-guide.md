# C Programming Syntax and Concepts – Revision Guide

## Table of Contents
1. [Introduction to C](#1-introduction-to-c)
2. [Basic Syntax and Program Structure](#2-basic-syntax-and-program-structure)
3. [Data Types and Variables](#3-data-types-and-variables)
4. [Operators](#4-operators)
5. [Control Flow Statements](#5-control-flow-statements)
6. [Functions](#6-functions)
7. [Pointers & Memory Management](#7-pointers--memory-management)
8. [Arrays & Strings](#8-arrays--strings)
9. [Structures, Unions & Enums](#9-structures-unions--enums)
10. [Preprocessor Directives](#10-preprocessor-directives)
11. [File Input / Output](#11-file-input--output)
12. [Dynamic Memory Allocation](#12-dynamic-memory-allocation)
13. [Common Errors & Debugging Tips](#13-common-errors--debugging-tips)
14. [Best Practices](#14-best-practices)
15. [Quick-Reference Cheat Sheet](#15-quickreference-cheat-sheet)

---

## 1. Introduction to C
* General-purpose, procedural language created by Dennis Ritchie (1972). Still dominant for system software, embedded firmware and performance-critical code [85][95].
* Compiles to small, fast binaries and offers low-level memory access via *pointers*.

---

## 2. Basic Syntax and Program Structure
```c
#include <stdio.h>     /* preprocessor directive */

int main(void)          /* main – program entry */
{
    printf("Hello C!\n");
    return 0;           /* exit status to OS */
}
```
Key points:
* Statements end with semicolon `;` [82].
* Braces `{}` delimit blocks; they must balance [146].
* Every program has exactly one `main()` [85].
* Comments: `/* ... */` (multi-line) or `//` (C99 single-line).
* `#include` inserts header files at compile time [144].

---

## 3. Data Types and Variables
| Category | Keyword(s) | Bytes* | Example |
|----------|-----------|--------|---------|
| Integer  | `char`, `short`, `int`, `long`, `long long` | 1-8 | `int count = 42;` |
| Unsigned | `unsigned int`, etc. | same | `unsigned long size;` |
| Floating | `float`, `double`, `long double` | 4-16 | `double pi = 3.1415;` |
| Other    | `_Bool`, `void`, pointers | – | `void *ptr;` |

*Sizes are implementation-dependent – use `sizeof()` to query at compile time [117].*

Constants & qualifiers:
```c
const double G = 9.81;
volatile int *hw_reg;
```

---

## 4. Operators
1. **Arithmetic** `+  -  *  /  %` [108]
2. **Assignment** `=  +=  -=  *=  /=  %=  <<=  >>=  &=  ^=  |=`
3. **Relational** `==  !=  >  <  >=  <=`
4. **Logical** `&&  ||  !` [106]
5. **Bitwise** `&  |  ^  ~  <<  >>` [103][115]
6. **Ternary** `?:`  `result = cond ? a : b;`
7. **Increment / Decrement** `++  --`
8. **Comma** `,`  evaluates left-to-right.

Operator precedence and associativity matter; use parentheses to be explicit [118].

---

## 5. Control Flow Statements
### 5.1 Selection
```c
if (expr) { ... } else { ... }

switch (ch) {
   case 'A': ...; break;
   default : ...;
}
```
`switch` is preferable to long `else-if` ladders for multi-way branches [104][119].

### 5.2 Loops
```c
while (condition) {...}
do { ... } while (condition);
for (init; test; update) {...}
```
Use `break` to exit and `continue` to skip rest of iteration [113].

### 5.3 Jump
`return`, `goto`, `break`, `continue` – use `goto` sparingly.

---

## 6. Functions
```c
/* prototype */
int add(int a, int b);

int add(int a, int b) {       /* definition */
    return a + b;
}
```
* 4 signature categories [128][134]:
  * with/without parameters; with/without return value.
* Parameters passed **by value**; use pointers for pass-by-reference.
* Forward declarations (prototypes) enable one-pass compilation.
* Variadic: `int printf(const char *fmt, ...);`

Inline documentation eases maintenance.

---

## 7. Pointers & Memory Management
```c
int value = 10;
int *p = &value;   /* address-of &  */
int  x = *p;       /* dereference * */
```
* Pointer types must match the pointee [135].
* `NULL` (or `nullptr` in C++) marks an invalid pointer.
* **Pointer arithmetic** advances in units of target size, not bytes.

### Dynamic Memory
```c
int *arr = malloc(n * sizeof *arr);  /* allocate */
arr = realloc(arr, m * sizeof *arr);
free(arr);                           /* release  */
```
Always check the return of `malloc` for `NULL` and `free()` exactly once to avoid leaks & double-free errors [129][141].

---

## 8. Arrays & Strings
```c
int a[5] = {1,2,3,4,5};
char msg[] = "Hello";   /* last char is '\0' */
```
* Arrays are contiguous; index origin is 0 [102].
* Array decays to pointer to first element when passed to functions.
* Common string functions: `strlen`, `strcpy`, `strncpy`, `strcat`, `strcmp` (`<string.h>`).
* Beware of **buffer overflows** – never use unchecked `gets()`; prefer `fgets()` or `snprintf()` [152].

Multidimensional:
```c
int m[3][4];          /* rows × cols */
```

---

## 9. Structures, Unions & Enums
```c
struct Point {
    int x, y;
};
struct Point p1 = {10, 20};
```
* `struct` groups heterogeneous data; members stored sequentially (may include padding) [124].
* `union` overlays members to share the same memory – only one member valid at a time [130][136][127].
* `enum Color { RED, GREEN, BLUE };` assigns integer constants.
* Use `typedef` for cleaner syntax:
```c
typedef struct Point Point;
Point p;
```

---

## 10. Preprocessor Directives
| Directive | Purpose |
|-----------|---------|
| `#include` | File inclusion [144] |
| `#define`  | Define macro or constant [150][153] |
| `#undef`   | Undefine macro |
| `#ifdef / #ifndef / #if / #else / #endif` | Conditional compilation [156] |
| `#pragma`  | Compiler-specific commands |

Example:
```c
#define MAX 100
#ifndef VERSION
  #define VERSION "1.0"
#endif
```

---

## 11. File Input / Output
```c
FILE *fp = fopen("data.txt", "r");
if (!fp) { perror("open"); return 1; }

int num; fscanf(fp, "%d", &num);
fprintf(stdout, "Read: %d\n", num);
fclose(fp);
```
Main modes: `"r"`, `"w"`, `"a"`, add `+` for read/write, `b` for binary [145][142][148].

High-level I/O: `fscanf`, `fprintf`, `fgets`, `fputs`, `fread`, `fwrite` [151][154].

---

## 12. Dynamic Memory Allocation
* **Heap functions** in `<stdlib.h>`:
  * `malloc`, `calloc`, `realloc`, `free` [129].
* `calloc` zero-fills; `malloc` leaves memory uninitialized.
* Always pair every allocation with a corresponding `free` to avoid leaks [152].
* Tools: Valgrind, AddressSanitizer.

---

## 13. Common Errors & Debugging Tips
| Category | Example & Symptom | Fix | Source |
|----------|------------------|-----|--------|
| Compile-time | Missing `;` → *expected ‘;’* | Add terminator | [146] |
| Linker | Undefined reference to symbol | Link correct object/library | [155] |
| Runtime | Segmentation fault from dereferencing `NULL` | Check pointer before use | [143] |
| Logic | `=` instead of `==` in `if` | Use `==` for comparison | [146] |
| Buffer overflow | Writing past array bounds | Validate lengths / use safe APIs | [152] |
| Memory leak | `malloc` without `free` | Track allocations, run valgrind | [152] |

General checklist:
* Compile with **`-Wall -Wextra -pedantic`**.
* Step through with `gdb`; set breakpoints and inspect variables.
* Use assertions `assert(expr)` to catch invalid states ( `<assert.h>` ).

---

## 14. Best Practices
* Initialize every variable upon declaration [146].
* Prefer `static` for internal linkage; avoid unnecessary globals.
* Use `const` to express intent and allow compiler optimizations.
* Limit function length (<50 lines) and keep modules cohesive.
* Handle all `switch` `default` cases and check I/O function returns.
* Document with clear comments and meaningful identifiers.

---

## 15. Quick-Reference Cheat Sheet
```c
/* Basic Types */
int, long, short, char, float, double, _Bool, void
unsigned int u;  size_t n;  ptrdiff_t d;

/* Control */
for(init; cond; ++i){}
while(cond){}
if(expr){} else {}
switch(x){case A: break; default:}

/* Memory */
T *p = malloc(N*sizeof *p);
free(p);

/* I/O */
FILE *fp = fopen("file", "r");
fscanf(fp, "%d", &num);
fclose(fp);

/* Preprocessor */
#define PI 3.14159
#ifdef DEBUG
    fprintf(stderr,"debug\n");
#endif

/* Pointer Tricks */
*(arr + i) == arr[i];
(*pp)++;   /* increment what pointer points to */
```

---

### End of Guide

Keep practicing small programs daily and refer to this sheet during quick revisions before interviews or exams. Happy coding!
