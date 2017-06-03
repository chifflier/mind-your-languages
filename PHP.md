Table of Contents
=================

   * [PHP](#php)
      * [Language gotchas](#language-gotchas)
         * [PHP collisions](#php-collisions)
      * [Code injection](#code-injection)
         * [String evaluation](#string-evaluation)
         * [Variable evaluation](#variable-evaluation)

# PHP

## Language gotchas

---

### PHP collisions

```php
$s1='QNKCDZO'; $h1=md5($s1);
$s2='240610708'; $h2=md5($s2);
$s3='A169818202'; $h3=md5($s3);
$s4='aaaaaaaaaaaumdozb'; $h4=md5($s4);
$s5='badthingsrealmlavznik'; $h5=sha1($s5);

if ($h1==$h2) print("Collision\n");
if ($h2==$h3) print("Collision\n");
if ($h3==$h4) print("Collision\n");
if ($h4==$h5) print("Collision\n");
```

Expected result: nothing

Result: `Collision` displayed 4 times

Reason: When comparing two strings with `==`, the interpreter test if they can be casted to a number.
Here, every hash results in a string of the form: some zeros, an 'e', and many numbers.
When comparing using `==`, PHP convert the values to floating-point numbers, all representing 0,
so all comparisons are true.

**This is often used when comparing passwords, and be a security vulnerability**

Solution: use the `===` operator.

Tested with: PHP 5, PHP 6

---


## Code injection

This category does not represent vulnerabilities or unexpected behaviors.
It shows different ways of evaluating code dynamically.

---

### String evaluation

```php
$cmd1="echo 'Hello ".$val."\n';";

eval($cmd1);
```

`eval` is the most basic function to evaluate a string.

---

### Variable evaluation

```php
function hello() { echo "Hello world<br />"; }
function goodbye() { echo "Good bye<br />"; }

$x="hello"; $x();

$y="x"; $$y="goodbye"; $x();
```

Variables allow to call functions dynamically, without having to write `eval`.

---

