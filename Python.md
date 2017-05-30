Table of Contents
=================

   * [Python](#python)
      * [Unexpected and surprising](#unexpected-and-surprising)
         * [Default variables and references](#default-variables-and-references)

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

