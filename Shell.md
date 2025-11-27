Table of Contents
=================
<!--ts-->
* [Table of Contents](#table-of-contents)
* [Shell](#shell)
   * [Cursed code](#cursed-code)
      * [Multi-line shebang for C programs](#multi-line-shebang-for-c-programs)
<!--te-->

# Shell

## Cursed code

---

### Multi-line shebang for C programs

C code is not interpreted, but with some work you can write a (kind-of) multi-line shebang to interpret it:
```sh
#if 0
TMP="$(mktemp -d)"
cc -o "$TMP/a.out" -x c "$0" && "$TMP/a.out" $@
RVAL=$?
rm -rf "$TMP"
exit $RVAL
#endif

#include <stdio.h>
int main() {
  puts("This is such a cool bash script!");
}
```

Run this using
```
chmod +x shebang.c
./shebang.c
```

Result: `This is such a cool bash script!`

Reason: the first line is not a shebang, so when the shell tries to execute it, it will fail. In such case, most common shells guess that it may be a sub script, and then execute it in a subshell. While the `#if 0` part is `false` in C, it is `true` in shell, so it is used to make the C preprocessor ignore that part while the shell will execute it

Tested with: zsh 5.9, bash 5.3.3

Credit: <https://apaz.dev/blog/Cursed_Code_Collection.html>

---

