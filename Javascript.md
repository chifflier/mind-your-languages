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

