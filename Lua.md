Table of Contents
=================
<!--ts-->
* [Table of Contents](#table-of-contents)
* [Lua](#lua)
   * [Weird behavior](#weird-behavior)
      * [True, False, or not](#true-false-or-not)
   * [Interesting facts](#interesting-facts)
      * [Arrays start at index 1](#arrays-start-at-index-1)
<!--te-->

# Lua

## Weird behavior

---

### True, False, or not

```lua
> 0 == false
false
> 0 == true
false
```

Expected result: type error, or a consistent type conversion

Result: false

While comparing a boolean and an integer is wrong, raising a type error would
be a better idea than returning `false`.

Tested with: Lua 5.4.8

---

## Interesting facts

### Arrays start at index 1

```lua
> a = {0, 1, 2}
> a[1]
0
> a[0]
nil
```

Starting at index 1 is not wrong by itself, and totally up to the language developers.
However, this will typically be a source of problems for many programmers.

Tested with: Lua 5.4.8

---

