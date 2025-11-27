Table of Contents
=================
<!--ts-->
* [Table of Contents](#table-of-contents)
* [Rust](#rust)
   * [Undefined behaviors](#undefined-behaviors)
      * [Floating point to integer casts](#floating-point-to-integer-casts)
   * [Memory error](#memory-error)
      * [Pattern guard consuming value being matched](#pattern-guard-consuming-value-being-matched)
   * [Cursed/Weird code](#cursedweird-code)
      * [Quote each token](#quote-each-token)
   * [Expected result: uh](#expected-result-uh)
<!--te-->

# Rust

## Undefined behaviors

### Floating point to integer casts

```rust
fn main() {
    let a = 360.0f32;
    println!("{:?}", a as u8);
    
    let a = 360.0f32 as u8;
    println!("{:?}", a);
    
    println!("{:?}", 360.0f32 as u8);
}
```

Expected result: 3x the same (despite the `u8` overflow)

Results:
```
104  // this is consistent between runs
208  // this varies on every single run!
255  // this is consistent between runs
```

Tested with:
 - rustc 1.33.0
 - rustc 1.35.0-nightly

See also: https://github.com/rust-lang/rust/issues/10184

## Memory error

---

### Pattern guard consuming value being matched

```rust
fn main() {
    let a = Some("...".to_owned());
    let b = match a {
        Some(_) if { drop(a); false } => None,
        x                             => x,
    };
    println!("{:?}", b);
}
```

Expected result: build error (matched value `a` is dropped in pattern guard)

Result: builds fine, and prints random values or crashes

Reason:

Tested with:
 - rustc 1.18.0 (03fc9d622 2017-06-06)
 - rustc 1.20.0-nightly (859c3236e 2017-06-26)

See also: https://github.com/rust-lang/rust/issues/31287

---

## Cursed/Weird code

This section contains code that is not inherently wrong, but is quite hard to read, convoluted, or strange in other ways.

### Quote each token

Macros can quickly become _very_ hard to read or understand
```rust
macro_rules! quote_each_token {
    ($tokens:ident $($tts:tt)*) => {
        $crate::quote_tokens_with_context!{$tokens
            (@ @ @ @ @ @ $($tts)*)
            (@ @ @ @ @ $($tts)* @)
            (@ @ @ @ $($tts)* @ @)
            (@ @ @ $(($tts))* @ @ @)
            (@ @ $($tts)* @ @ @ @)
            (@ $($tts)* @ @ @ @ @)
            ($($tts)* @ @ @ @ @ @)
        }
    };
}
```
This is [real code](https://github.com/dtolnay/quote/blob/3256f897c9821f9b397cb81851ae9a1672a84cb7/src/lib.rs#L759-L841)  from the `quote` crate! The link includes an explanation of how the macro works.

Expected result: uh

---
