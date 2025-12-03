<!--ts-->
* [Python](#python)
   * [Unexpected and surprising](#unexpected-and-surprising)
      * [Default variables and references](#default-variables-and-references)
      * [mutatis mutandis tuples](#mutatis-mutandis-tuples)
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

### mutatis mutandis tuples

Tuples are immutable objects, so a modification attempt will raise an exception. However,
in some cases, the modification is done even with the exception:
```python
>>> t=([1],2)
>>> t[0]+=[3]
Traceback (most recent call last):
  File "<python-input-13>", line 1, in <module>
    t[0]+=[3]
    ~^^^
TypeError: 'tuple' object does not support item assignment
>>> t
([1, 3], 2)
```

Expected result: `([1], 2)`

Result: `([1, 3], 2)`

Reason:
- This is due to the order of operations (`iadd` then `assign`), and the fact that the read-only attribute is checked only when assigning
- The first item is a list, and is first extended with success. This does not create a new object,
but modifies the existing list.
- When the list value is attempted to be assigned to the first tuple item, the exception is raised

This is even [documented in the FAQ](https://docs.python.org/3/faq/programming.html#why-does-a-tuple-i-item-raise-an-exception-when-the-addition-works)

Tested with: Python 3.13.7

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

