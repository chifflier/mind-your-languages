Table of Contents
=================

   * [Javascript](#javascript)
      * [Language gotchas](#language-gotchas)
         * [Javascript breaks transitivity](#javascript-breaks-transitivity)
         * [Parsing integers](#parsing-integers)

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

