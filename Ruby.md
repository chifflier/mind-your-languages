Table of Contents
=================

   * [Ruby](#ruby)
      * [Interpreter](#interpreter)
         * [Parenthesis matters](#parenthesis-matters)
         * [End-of-line matters](#end-of-line-matters)
      * [Poisoning](#poisoning)
         * [The many ways to compare an integer](#the-many-ways-to-compare-an-integer)

# Ruby

## Interpreter

---

### Parenthesis matters

```ruby
> def test(x); x*2; end
= > :test

> test 1
=> 2
> test(1)
=> 2
> test(1)+1
=> 3
> test (1)+1
=> 4
```

In some cases, the whitespaces and parenthesis are interpreter differently, resulting in priority change in
the evaluation of the expression.

Tested with: irb 2.3

---

### End-of-line matters

```ruby
def test1 (x,y)
  x +
  y
end

def test2 (x,y)
 x
 + y
end

test1(1,2)
test2(1,2)
```

Expected result: `3`, `3`

Result: `3`, `2`

Reason: in `test1`, the `+` operator is before an EOL, so the interpreter assumes the line is continued after.
In `test2`, the `+ y` is interpreter as the unary `+` operator applied to `y`, to `x` is ignored and this function
always returns `y`.

Tested with: irb 2.3

---


## Poisoning

---

### The many ways to compare an integer

```ruby
> 3==4
=> false
> 3.eql?4
=> false
> 3.equal?4
=> false

> class Fixnum; def ==(y); true; end; end
=> nil
> 3==4
=> true
> 3.eql?4
=> true
> 3.equal?4
=> false
```

There are many ways to compare a value.
When overridind the `==` operator for a class, the comparisons are changed, but not all of them.

Tested with: irb 2.3

---

