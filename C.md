# C

## Errors

---

### Cast signed vs unsigned

```c
unsigned int a = 1; signed int b = -1;

if (a<b) printf("%d<%d\n",a,b);
else printf("%d>=%d\n",a,b);
```

Expected result: `1>=-1`

Result: `1<-1`

Reason: `a` is compared as a signed integer and wraps to a large value

Tested with: gcc 6.3.0, clang 3.9

---






## Tricks and less-known features

---

### And operator like Perl

The `&&` and `||` operators can be used in a statement like in Perl:
```c
(i>1) && printf("more than one argument\n");
```
---

### Pointer notation

Since `var[i] == *(var + i) == *(i + var)`, C accepts the notation `i[var]` as
equivalent:

```c
const char *hello = "hello, world";
int i = 4;

printf("char %d: %c\n", i, i[hello]);
printf("char %d: %c\n", i, i["hello, world"]);
```
---

### Preprocessor Abuse


The `#include` preprocessor directive can be used to interactively ask user a
value during the compilation:
```c
int i =
#warning Enter value for i:
#include "/dev/tty"
;
```
---







## The compiler is not your friend

---

### Read-only strings and segmentation faults

```c
char *hello = "hello, world";

hello[0] = 'Y';
hello[1] = 'o';
```

Expected result: warning or error

Result: no warning with `-Wall -Wextra -pedantic`, segmentation fault at runtime

Reason: `hello` is placed in the `.rodata` section and cannot be modified, so
this code is expected to always segfault. Adding the `-Wwrite-strings` flags
shows the problem, but this flags is not in `-Wall -Wextra -pedantic`.

Additionally, line 1 is an implicit cast from `const char []` to `char *`, and
the loss of the `const` attribute is not signalled by the compiler.

Tested with: gcc 6.3.0, clang 3.9

---
