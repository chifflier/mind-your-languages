Table of Contents
=================

   * [Javascript](#javascript)
      * [Language gotchas](#language-gotchas)
         * [Javascript breaks transitivity](#javascript-breaks-transitivity)

# Javascript

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

```javascript
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

---

