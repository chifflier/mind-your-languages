Table of Contents
=================
<!--ts-->
* [Table of Contents](#table-of-contents)
* [Lua](#lua)
   * [Weird behavior](#weird-behavior)
      * [True, False, or not](#true-false-or-not)
      * [Function returns](#function-returns)
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

### Function returns

```lua
> function f123() return 1, 2, 3 end
> function f456() return 4, 5, 6 end
> print(f123())
1	2	3
> print(f123(), f456())
1	4	5	6
```

Expected result: `1 2 3 4 5 6`

Reason: only one value is returned from a function if it's not the last one in a list. Here, the `printf` function will get two function returns, but only the last one will have the multiple values.
This is known and even [documented](https://www.lua.org/pil/5.1.html), though still questionable.

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

