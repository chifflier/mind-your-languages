Table of Contents
=================
<!--ts-->
* [Table of Contents](#table-of-contents)
* [C](#c)
   * [Errors](#errors)
      * [Cast signed vs unsigned](#cast-signed-vs-unsigned)
   * [Tricks and less-known features](#tricks-and-less-known-features)
      * [And operator like Perl](#and-operator-like-perl)
      * [Pointer notation](#pointer-notation)
      * [Unspecified order of function evaluation](#unspecified-order-of-function-evaluation)
      * [Preprocessor Abuse](#preprocessor-abuse)
      * [Brackets](#brackets)
      * [Conditionals with Omitted Operands](#conditionals-with-omitted-operands)
      * [URL in code?](#url-in-code)
      * [Shortest program in C](#shortest-program-in-c)
      * [Line-continuation in comments and code](#line-continuation-in-comments-and-code)
      * [More symbols](#more-symbols)
      * [switch/case](#switchcase)
   * [The compiler is not your friend](#the-compiler-is-not-your-friend)
      * [Read-only strings and segmentation faults](#read-only-strings-and-segmentation-faults)
   * [Undefined and unspecified behaviors](#undefined-and-unspecified-behaviors)
      * [Character sign](#character-sign)
      * [Integer size](#integer-size)
      * [Bitwise shift operators](#bitwise-shift-operators)
      * [Function calls and arguments](#function-calls-and-arguments)
      * [Floating-point exception with integers](#floating-point-exception-with-integers)
      * [Strict aliasing](#strict-aliasing)
<!--te-->

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

Reason: According to the *usual arithmetic conversions* specified in C11 §6.3.1.8, operand `b` is implicitly converted to an expression of type `unsigned int` and thus evaluates to a large value.

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

### Unspecified order of function evaluation

What is the order of evaluation of functions `f, g, h, i, j`?
```c
f(g(h()), i(j()))
```

All we can say is:
- `h` will be evaluated before `g`
- `j` will be evaluated before `i`
- `g` and `i` will be evaluated before `f`
- we cannot, for example, know if `j` is evaluated before or after `h`

The order of function evaluation in the same statement is unspecified. Extract
from the C specification (section 5.2.2): `The order of evaluation of arguments is unspecified.
All side effects of argument expression evaluations take effect before the
function is entered.`

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

### Brackets

The following code compiles and runs successfully:
```c
#include<stdio.h>
int main()
<%
    int arr <:10:>;
    arr<:0:> = 1;
    printf("%d", arr<:0:>);

    return 0;
%>
```

- `<% %>` can be used in place of `{ }`
- `<: :>` can be used in place of `[ ]`
- these are _not_ trigrams!

Tested with: gcc 15.2.1 (`-Wall -Wextra`)

---

### Conditionals with Omitted Operands

Although illegal in standard C, [a GNU extension](https://gcc.gnu.org/onlinedocs/gcc/Conditionals.html) makes it possible to omit the middle operand in a conditional expression when using the ternary operator. If the condition evaluates to true, the value of the conditional expression is then that of the first operand, i.e. the value of the condition itself. The following two expressions are thus equivalent in GNU dialect of ISO C (e.g. when compiling with `-std=gnu89`):
```c
x ?: y
x ? x : y
```

This extension is particularly useful when the evaluation of the condition has side effects, as it avoids performing them twice. However, this can make ternary conditions more difficult to read:
```c
/* From Linux v5.4-rc5 (https://elixir.bootlin.com/linux/v5.4-rc5/source/drivers/gpu/drm/i915/i915_active.c#L80) */
return (void *)ref->active ?: (void *)ref->retire ?: (void *)ref;

/* From Linux v5.4-rc5 (https://elixir.bootlin.com/linux/v5.4-rc5/source/net/rxrpc/local_object.c#L39) */
diff = ((local->srx.transport_type - srx->transport_type) ?:
        (local->srx.transport_len - srx->transport_len) ?:
        (local->srx.transport.family - srx->transport.family));
```
and even more prone to type errors, as the condition must be type-compatible with the third operand.

---

### URL in code?

The followowing code compiles and runs successfully:
```c
#include <stdio.h>

int main(void)
{
    https://i_am_an_url.net/
    printf("hello, world\n");
    return 0;
}
```

The reasons are:
- comments starting with `// ` are valid in C99
- the first part (`http:`) is interpreted as a label

---

### Shortest program in C

The shortest program in C compiling with default compiler flags:
```c
int main;
```

Tested with: gcc 15.2.1

_Note: if you have any other solution, please submit it!_

---

### Line-continuation in comments and code

The followowing comment and variable definitions are accepted:
```c
int main() {
    /\
/ Line Com\
ment
    i\
n\
t \
a\
b\
c\
;

    return 0;
}
```

The line continuation character is allowed in comments, and even in tokens,
keywords and variable names.

---

### More symbols

The parser can refuse valid constructions (ambiguous grammar).
```c
x+++y; // this is valid and accepted: x++ + y;
x+++++y; // this is valid but not accepted: x++ + ++y;
```

---

### switch/case

Inside a `switch` statement, the `case` lines are labels, and they can be
interleaved with other instructions (like loops):
```c
int i = 0;
switch (n) {
    do {
        case 1:
            i++;
    }while (i < 10);
    case 2:
        i=3;
    default:
        i--;
}
```

The code above compiles (even if it has no particular meaning). The `case` statements acts as labels that are also targets for `goto` statements.

The most common use case is a way of unrolling loops named [Duff's device](https://en.wikipedia.org/wiki/Duff's_device).

Given the poor readability of such code, one should avoid writing such code.

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

Reason: For historical reasons, string literals are of type `char[]` in C but
attempting to modify such an array is undefined behavior (C11 §6.4.5). In
practice, most compilers place string literals in the `.rodata` section,
therefore `hello` cannot be modified and this code is expected to always
segfault. Adding the `-Wwrite-strings` flag shows the problem, but this flag is
not in `-Wall -Wextra -pedantic`.

Tested with: gcc 6.3.0, clang 3.9

---



## Undefined and unspecified behaviors

---

### Character sign

The C standard does not specify if plain `char` is signed or unsigned, so the
size of a `char` is implementation defined. On some architectures it will be
signed, and unsigned on others.

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

### Function calls and arguments

The expressions passed as arguments of a function call are evaluated in an unspecified order.

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

### Floating-point exception with integers

The following code uses only integers:
```c
/* Note: using volatile to prevent the div to be optimized out */
volatile int int_min = INT_MIN;
volatile int minus_1 = -1;

printf("%d\n", int_min / minus_1);
```

This code will reliably trigger a runtime error (on `x86/x64` platforms):
```
[1]    211660 floating point exception (core dumped)  ./a.out
```

When dividing the smallest negative integer by `-1`, the result _should_
be positive, but in fact cannot be represented using two's complement (it would
be `INT_MAX + 1`), so it is undefined behavior.

Cause:
- on `x86` platforms, the [`idiv`](https://www.felixcloutier.com/x86/idiv) operation will raise a division error `#DE`
- the error is caught by the operating system, which signals it to the program
- when an error occurs in integer operations, POSIX requires it to be `SIGFPE`

This is platform-dependant, and will not raise the same error on ARM hardware,
for example.

Note: this does _not_ happen when multiplying by `-1`. The code `INT_MIN * -1`
will also overflow, but in that case the overflow is silently ignored.

Note 2: the result is the same when using the `div` function from `stdlib.h`

Tested with: gcc 15.2.1, clang 21.1.5

---

### Strict aliasing

The following code gives different results, depending on the optimization level:
```c
#include <stdio.h>

long foo(int *x, long *y) {
  *x = 0;
  *y = 1;
  return *x;
}

int main(void) {
  long l;
  printf("%ld\n", foo((int *)&l, &l));
}
```

The output is the following:
```
$ gcc strict-aliasing.c
$ ./a.out
1
$ gcc -O2 strict-aliasing.c
$ ./a.out
0
```

Cause:
- this code violates the _strict aliasing rule_, and creates aliased pointers
- when compiling with `-O2`, the compiler makes different assumptions when optimizations are enabled

This is a common source of undefined behaviors, especially in code
aliasing for example a `&uint64_t` with a `uint32_t *` pointer.

The following function illustrates this:
```c
uint32_t swap_words(uint32_t arg)
{
  uint16_t* const volatile sp = (uint16_t*)&arg;
  uint16_t        hi = sp[0];
  uint16_t        lo = sp[1];

  sp[1] = hi;
  sp[0] = lo;

  return (arg);
}
```

This code is wrong, and will return different results depending on the optimization level:
```
$ # input value is 0xabcd1234
$ gcc -Wall -Wextra strict-aliasing-uint32.c
$ ./a.out
x=abcd1234
y=1234abcd

$ gcc -O2 -Wall -Wextra strict-aliasing-uint32.c
$ ./a.out
x=abcd1234
y=abcd1234
```

Solution: do not use pointer aliasing!

The following compiler flags can help detecting aliasing: `-fstrict-aliasing -Wstrict-aliasing=2`:
```
strict-aliasing-uint32.c: In function ‘swap_words’:
strict-aliasing-uint32.c:7:44: warning: dereferencing type-punned pointer will break strict-aliasing rules [-Wstrict-aliasing]
    7 |   uint16_t* const volatile sp = (uint16_t*)&arg;
      |
```

Other resources:
- [Understanding Strict Aliasing](https://cellperformance.beyond3d.com/articles/2006/06/understanding-strict-aliasing.html)
- [The Strict Aliasing Situation is Pretty Bad](https://blog.regehr.org/archives/1307)

Tested with: gcc 15.2.1, clang 21.1.5

---
