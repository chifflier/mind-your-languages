Table of Contents
=================

   * [Lua](#lua)
      * [Weird behavior](#weird-behavior)
         * [True, False, or not](#true-false-or-not)

# Lua

## Weird behavior

---

### True, False, or not

```lua
> return  1 == true
false
```

Expected result: type error, or true

Result: false

While comparing a boolean and an integer is wrong, raising a type error would
be a better idea than returning `false`.

Tested with: Lua 5.1.5

---

