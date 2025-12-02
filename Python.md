<!--ts-->
* [Python](#python)
   * [Unexpected and surprising](#unexpected-and-surprising)
      * [Default variables and references](#default-variables-and-references)
      * [You spin me round round](#you-spin-me-round-round)
<!--te-->

# Python

Other resources:
- [wtfPython](https://github.com/satwikkansal/wtfPython)

## Unexpected and surprising

---

### `is` is not equal

```python
>>> a = 256
>>> b = 256
>>> a is b
True

>>> a = 257
>>> b = 257
>>> a is b
False
```

Reason: the `is` operator tests if the operands refer to the _same_ objects.
Internally, CPython keeps an array for [integers between -5 and
256](https://docs.python.org/3/c-api/long.html), so cached objects are returned
for values within this range, and new objects for other values.

The correct operator to use here is `==`, which returns the expected results.

Tested with: Python 3.7

Note: Python has added checks since the tested version and redirect to the `==` operator, for ex. with Python 3.13:
```python
>>> 257 is 257
<python-input-1>:1: SyntaxWarning: "is" with 'int' literal. Did you mean "=="?
True
```

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

