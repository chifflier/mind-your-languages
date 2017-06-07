# Java

## Language gotchas

---

### Overloaded methods and type promotions

Implicit promotions can give interesting results when combined with overloaded methods:

```java
class Confuser {

  static void A(short i) { System.out.println("Foo"); }
  static void A(int i) { System.out.println("Bar"); }

  public static void main (String[] args) {
    short i=0;
    A(i);
    A(i+i);
    A(i+=i);
  }
}
```
Some operations will cause a promotion to a greater type, calling a different method.

---

### Equality and integers

```java
Integer a1=42;
Integer a2=42;
if (a1==a2) System.out.println("a1 == a2");

Integer b1=1000;
Integer b2=1000;
if (b1==b2) System.out.println("b1 == b2");
```

Expected result: `a1 == a2` and `b1 == b2`

Result: `a1 == a2`

Reason: The `==` operator tests the equality of objects, not values. The first 128 integers are stored in a cache,
so the creation of the object returns the cached value, and the test succeeds.

Solution: use the `.equal` method

Note: Using reflection, the cached values can even be poisoned

Tested with:

---

### Serialization and static methods

```java
import java.io.*;
class Friend { } // Unlikely to be dangerous!
class Deserial {
  public static void main (String[] args)
    throws FileNotFoundException, IOException,
           ClassNotFoundException {
    FileInputStream fis = new FileInputStream("friend");
    ObjectInputStream ois = new ObjectInputStream(fis);
    Friend f=(Friend)ois.readObject();
    System.out.println("Hello world");
  }
}
```

The code looks OK, but
if the file `friend` contains a serialized class with static methods, they will be executed *before* testing the
cast to the `Friend` object, leading to executed code before the failed cast.

---







## Source code

---

### UTF-8

```java
public class Preprocess {
  public static void ma\u0069n (String[] args) {
    if (false==true)
    { //\u000a\u007d\u007b
      System.out.println("Bad things happen!");
    }
  }
}
```

Expected result: nothing

Result: `Bad things happen!`

Reason: UTF-8 sequences are processed *before* compiling. This sequence contains a line end (for the comment), and
brackets characters to end the `if` block and start a new one.

Tested with:

---

### Inner classes and attributes

Inner classes are supported by the source language, but not by the bytecode.
As a result, the `private` qualifier of attributes is ignored in inner classes:

```java
public class Innerclass {
  private static int a=42;

  static public class Innerinner {
    private static int b=54;
    public static void print() {
      System.out.println(Innerclass.a);
    }
  }

  public static void main (String[] args) {
    System.out.println(Innerinner.b);
    Innerinner.print();
  }
}
```

---
