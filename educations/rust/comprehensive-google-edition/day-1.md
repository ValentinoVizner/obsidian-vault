# Day 1
---
- Basic Rust syntax: variables, scalar and compound types, enums, structs, references, functions, and methods.
- Memory management: stack vs heap, manual memory management, scope-based memory management, and garbage collection.

- Ownership: move semantics, copying and cloning, borrowing, and lifetimes.

Small example of Rust code:
```rust
fn main() {              // Program entry point
    let mut x: i32 = 6;  // Mutable variable binding
    print!("{x}");       // Macro for printing, like printf
    while x != 1 {       // No parenthesis around expression
        if x % 2 == 0 {  // Math like in other languages
            x = x / 2;
        } else {
            x = 3 * x + 1;
        }
        print!(" -> {x}");
    }
    println!();
}
```
---
## Why Rust?
- Rust strings are UTF-8 encoded and can contain any Unicode character. (example: 🌍)
- Rust is used for a wide range of devices:
    -   firmware and boot loaders,
    -   smart displays,
    -   mobile phones,
    -   desktops,
    -   servers.
- Some unique selling points of Rust:
  -   Compile time memory safety.
  -   Lack of undefined runtime behavior.
  -   Modern language features.

### Compile Time Guarantees

Static memory management at compile time:
-   No uninitialized variables.
-   No memory leaks (_mostly_, see notes).
-   No double-frees.
-   No use-after-free.
-   No `NULL` pointers.
-   No forgotten locked mutexes.
-   No data races between threads.
-   No iterator invalidation.

### Runtime Guarantees

No undefined behavior at runtime:

-   Array access is bounds checked.
-   Integer overflow is defined.

# Scalar Types

|                 | Types                                      | Literals                      |
| --------------- | ------------------------------------------ | ----------------------------- |
| Signed integers | `i8`, `i16`, `i32`, `i64`, `i128`, `isize` | `-10`, `0`, `1_000`, `123i64` |
| Unsigned integers | `u8`, `u16`, `u32`, `u64`, `u128`, `usize`  |   `0`, `123`, `10u16` |
| Floating point numbers | `u8`, `u16`, `u32`, `u64`, `u128`, `usize` |`f32`, `f64`|
| Strings| `&str` |`"foo"`, `r#"\\"#`|
|Unicode scalar values|`char`|`'a'`, `'α'`, `'∞'`|
|Byte strings|`&[u8]`|`b"abc"`, `br#" " "#`|
|Booleans|`bool`|`true`, `false`|



The types have widths as follows:

-   `iN`, `uN`, and `fN` are _N_ bits wide,
-   `isize` and `usize` are the width of a pointer,
-   `char` is 32 bit wide,
-   `bool` is 8 bit wide.