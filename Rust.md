Table of Contents
=================

   * [Rust](#rust)
      * [Memory error](#memory-error)
         * [Pattern guard consuming value being matched](#pattern-guard-consuming-value-being-matched)

# Rust

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

