<!--ts-->
* [Python](#python)
   * [Unexpected and surprising](#unexpected-and-surprising)
      * [Default variables and references](#default-variables-and-references)
      * [You spin me round round](#you-spin-me-round-round)
<!--te-->

# Python

## Unexpected and surprising

---

### Default variables and references

```python
def foo(a=[]):
    a.append(5)
    return a

print(foo())
print(foo())
```

Expected result: `[5]` and `[5]`

Result: `[5]` and `[5,5]`

Reason: default arguments are passed by reference, so if changed, the default argument also changes.

Tested with: Python 2.7, Python 3.6

---

### You spin me round round

```python
>>> round(3.5)
4
>>> round(4.5)
4
```

Expected result: ?

Reason: Python rounds floating values to the nearest even integer, known as [rounding half to even](https://en.wikipedia.org/wiki/Rounding#Rounding_half_to_even) (or Banker's Rounding).

Note: this method is generally used to minimize errors when summing numbers.

Tested with: Python 3.13

---

