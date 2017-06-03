Table of Contents
=================

   * [OCaml](#ocaml)
      * [Poisoning](#poisoning)
         * [Strings are passed by reference](#strings-are-passed-by-reference)

# OCaml

## Poisoning

---

### Strings are passed by reference

```ocaml
let check c =
  if c then "OK" else "KO";;

let f=check false in
  f.[0]<-'O'; f.[1]<-'K';;

check true;;
check false;;
```

Expected result: `OK` then `KO`

Result: `OK` twice

Reason: the returned value from `check` is a reference to the string, and can be modified.
The code in lines 4-5 change `KO` to `OK`.

Solution: use the `-safe-string` compiler flag

Tested with: OCaml 4.02.3

---

