Table of Contents
=================
<!--ts-->
* [Table of Contents](#table-of-contents)
* [Javascript](#javascript)
   * [Language gotchas](#language-gotchas)
      * [Javascript breaks transitivity](#javascript-breaks-transitivity)
      * [No transitivity for comparison either](#no-transitivity-for-comparison-either)
      * [Parsing integers](#parsing-integers)
      * [Arrays are sometimes equal. Or not.](#arrays-are-sometimes-equal-or-not)
      * [Abstract and relational abstract comparisons](#abstract-and-relational-abstract-comparisons)
      * [Objects and string obfuscation](#objects-and-string-obfuscation)
<!--te-->

# Javascript

* Many other examples here: [wtfjs](http://wtfjs.com)

## Language gotchas

---

### Javascript breaks transitivity

```javascript
document.write("'0'",'0'==0?"==":"<>","0 and ");
document.write("0",0=='0.0'?"==":"<>","'0.0' and ");
document.write("'0'",'0'=='0.0'?"==":"<>","'0.0'<br />");
```

Expected result: `'0'==0 and 0=='0.0' and '0'=='0.0'`

Result: `'0'==0 and 0=='0.0' and '0'<>'0.0'`

Reason: Strings can be casted to integers, except if compared to another string

---

### No transitivity for comparison either

```javascript
1 < 2 < 3  // true
3 > 2 > 1  // false
1 < 2 < 1  // true
```

Reason:

This is caused by type conversion:
```javascript
1 < 2 < 3 // is evaluated to:
true < 3  // true -> 1
1 < 3     // -> true

3 > 2 > 1 // is evaluated to:
true > 1  // true -> 1
1 > 1     // -> false
```

---

### Parsing integers

The `parseInt` function seems to quite innovative:

```javascript
parseInt('2',10)
2
parseInt('null',10)
NaN
parseInt('null',23)
NaN
parseInt('null',24)
23
parseInt('infinity',20)
18
parseInt('infinity',28)
324267766
```

Expected result: `NaN`

Reason: ???

Theory:
In base 24, 0-9 are the first 10 digits. Then come the letters. And since "n" is the 14th letter, it has a value in base 24
Since "n" can be interpreted, it moves on to the next letter: "u". But "u" doesn't have a value in base 24, since there are only 24 digits!
So, the parseInt function just returns the integer value for what "n" represents in base 24 → 23.

This would work for the 'null' string, but has no explanation for the `null` object:
```javascript
parseInt(null,24)
23
```

Also, this seems to stop working at base 37:
```javascript
parseInt("null",36)
1112745
parseInt("null",37)
NaN
```

See https://tc39.github.io/ecma262/#sec-parseint-string-radix for the definition of `parseint`.

---

### Arrays are sometimes equal. Or not.

```javascript
> [] == ![]
true
```

Expected result: `false`

Reason:
- for some reason, javascript converts both sides to numbers before comparing them (while they have the same type)
- `[]` evaluates to `0`
- `![]` evaluates to `false`, which then evaluates to `0`

This also works for non-null arrays containing zeroes (`[0] == ![0]`).

However, this does not generalize, and gets worse:
```javascript
> [1]
[1]
> +[1]
1
> [1]==![1]
false
> [1]==[1]
false
> [1]==+[1]
true
```

Explanation:
- when comparing _objects_, `==` returns true only for the _same_ object
- when comparing different types, `==` first converts to the same type (so `[1]` can be converted to the number `1`) before comparison
- see [the comparisons algorithms](#equality-algorithm)

---

### Abstract and relational abstract comparisons

Comparisons using `==` and `>=` leads to suprising results when comparing objects:

```javascript
> [0]>=[0]
true
> [1]==[1]
false
> [1]>[1]
false
> [1]>=[1]
true
> [1]>=[0]
true
> [0]>=[1]
false
```

Expected result: ???

Reason:
<a name="equality-algorithm"></a>
- The equality operators (`==` and `!=`) use [The Abstract Equality Comparison Algorithm](https://262.ecma-international.org/5.1/#sec-11.9.3) for comparison, while relational operators (`>`, `<` etc.) use [The Abstract Relational Comparison Algorithm](https://262.ecma-international.org/5.1/#sec-11.8.5).
- `==` and `!` convert to the same type and then compare
- relational operators (`>`, `<` etc.) convert to primitive type then compare


Note: even if `==` is supposed to compare directly if the type is the same, this leads to other interesting results:
```javascript
> var a = [1]
> typeof(a)
"object"
> a == a
true
> a == [1]
false
> [1]==+a
true
```

This is caused by the comparison algorithm: for objects, it only returns `true` if both sides refer to the _same_ object.

### Objects and string obfuscation

Due to objects internal conversions, it is easy to generate strings using only objects and operators:
```javascript
> (![]+[])
"false"
```

Note the resulting type (string). This is caused by the `+` operator calling `ToPrimitive`, resulting in `[[DefaultValue]]` or the right operator, in this case returning an empty string. Since `![]` is evaluated to `false`, this gives `false+""` which is `"false"`

Combining with other techniques to generate indices, it is easy to access a single character:
```javascript
> (![]+[])[+[]]
"f"
```

If the letter can be indexed from a generated string, this is rather trivial.
More complex examples require encoding, but it can be done using 6 characters only (see "Writing a sentence without using the Alphabet" [Part 1](https://bluewings.github.io/en/writing-a-sentence-without-using-the-alphabet/#weird-javascript-generator) and [Part 2](https://bluewings.github.io/en/writing-a-sentence-without-using-the-alphabet-part-2/), and [this site](https://jsfuck.com/)).

---

