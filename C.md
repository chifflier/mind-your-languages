Table of Contents
=================

   * [C](#c)
      * [Errors](#errors)
         * [Cast signed vs unsigned](#cast-signed-vs-unsigned)
      * [Tricks and less-known features](#tricks-and-less-known-features)
         * [And operator like Perl](#and-operator-like-perl)
         * [Pointer notation](#pointer-notation)
         * [Preprocessor Abuse](#preprocessor-abuse)
      * [The compiler is not your friend](#the-compiler-is-not-your-friend)
         * [Read-only strings and segmentation faults](#read-only-strings-and-segmentation-faults)

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



## Undefined and unspecified behaviors

---

### Character size

The size of a `char` is implementation defined. On some architectures it will be signed, and unsigned on others.

Reference: ISO C99 section 6.2.5

Solution: use `stdint.h` types

---

### Integer size

The definition of the representation of integers is the following:

```
The rank of long long int shall be greater than the rank of long int, which
shall be greater than the rank of int, which shall be greater than the rank of short
int, which shall be greater than the rank of signed char.
```

Technically, a compiler with 17-bit integers is compliant with the specifications.

The same is also true for `long` and `long long` types.

Note that, unlike `char`, the integer types are all `signed` by default.

Reference: ISO C99 section 6.3.1.1

Solution: use `stdint.h` types

---

### Bitwise shift operators

Shifting by a negative number of a number of bits greater than the size of the type is undefined.

```c
unsigned long setBit1(int n)
{ return (1 << n); }

unsigned long setBit2(int n)
{ return ((unsigned long) 1 << n); }

void main () {
  printf ("%lu\n", setBit1 (33));
  printf ("%lu\n", setBit2 (33));
}
```

Reference: ISO C99 section 6.5.7

Solution: don't do that

---

---

### Function calls and arguments

The expressions passed as arguments of a function call are evaluated in an undefined order.

Example 1:
```c
{ int c=0; printf("%d %d\n",c++,c++); }

{ int c=0; printf("%d %d\n",++c,++c); }

{ int c=0; printf("%d %d\n",c=1,c=2); }
```

Results can be surprising:

- The first line prints `1 0`
- The second line prints `2 2`
- The third line prints `1 1`




Example 2:
```c
int f(int x,int y) { return (2*x)+y; }

int main(void) {
  int x=0;
  int y=f((x=1),(x=3));
  printf("x=%d\n",x);
  printf("y=%d\n",y);
  return 0;
}
```

Output:
```
x=1
y=3
```
Meaning that `x=1` was executed after `x=3` and passed as both arguments of function `f`.

Reference: ISO C99 section 6.5.2.2

Solution: don't do that

---
