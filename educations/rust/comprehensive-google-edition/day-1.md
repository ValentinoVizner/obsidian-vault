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

# Compound Types
|     | Types | Literals |
| --- | ----- | -------- |
|Arrays|`[T; N]`|`[20, 30, 40]`, `[0; 3]`|
|Tuples|`()`, `(T,)`, `(T1, T2)`, …|`()`, `('x',)`, `('x', 1.2)`, …|

Array assignment and access:

```rust
fn main() {
    let mut a: [i8; 10] = [23; 7];
    a[5] = 6;
    println!("a: {:?}", a);
}
```

Tuple assignment and access:
```rust
fn main() {
    let tuple: (i8, bool) = (0, false);
    println!("1st index: {}", t.0);
    println!("2nd index: {}", t.1);
}
```

# References
Like C++, Rust has references:
```rust
fn main() {
    let mut x: i32 = 10;
    let ref_x: &mut i32 = &mut x;
    *ref_x = 20;
    println!("x: {x}");
}
```

Some notes:

-   We must dereference `ref_x` when assigning to it, similar to C and C++ pointers.
-   Rust will auto-dereference in some cases, in particular when invoking methods (try `ref_x.count_ones()`).
-   References that are declared as `mut` can be bound to different values over their lifetime.

## Dangling References

Rust will statically forbid dangling references:
```rust
fn main() {
    let ref_x: &i32;
    {
        let x: i32 = 10;
        ref_x = &x;
    }
    println!("ref_x: {ref_x}");
}
```


-   A reference is said to “borrow” the value it refers to.
-   Rust is tracking the lifetimes of all references to ensure they live long enough.
-   We will talk more about borrowing when we get to ownership.

# Slices

A slice gives you a view into a larger collection:
```rust
fn main() {
    let array: [i32; 6] = [10, 20, 30, 40, 50, 60];
    println!("array: {array:?}");

    let slice: &[i32] = &a[2..4];
    println!("slice: {slice:?}");
}
```
-   Slices borrow data from the sliced type

# `String` vs `str`

We can now understand the two string types in Rust:
```rust
fn main() {
    let s1: &str = "World";
    println!("s1: {s1}");

    let mut s2: String = String::from("Hello ");
    println!("s2: {s2}");
    s2.push_str(s1);
    println!("s2: {s2}");
    
    let s3: &str = &s2[6..];
    println!("s3: {s3}");
}
```

Rust terminology:

-   `&str` an immutable reference to a string slice.
-   `String` a mutable string buffer.

# Functions
Fizzbuzz:
```rust
fn main() {
    fizzbuzz_to(20);   // Defined below, no forward declaration needed
}

fn is_divisible_by(lhs: u32, rhs: u32) -> bool {
    if rhs == 0 {
        return false;  // Corner case, early return
    }
    lhs % rhs == 0     // The last expression in a block is the return value
}

fn fizzbuzz(n: u32) -> () {  // No return value means returning the unit type `()`
    match (is_divisible_by(n, 3), is_divisible_by(n, 5)) {
        (true,  true)  => println!("fizzbuzz"),
        (true,  false) => println!("fizz"),
        (false, true)  => println!("buzz"),
        (false, false) => println!("{n}"),
    }
}

fn fizzbuzz_to(n: u32) {  // `-> ()` is normally omitted
    for i in 1..=n {
        fizzbuzz(i);
    }
}
```

## Methods
```rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    fn area(&self) -> u32 {
        self.width * self.height
    }

    fn inc_width(&mut self, delta: u32) {
        self.width += delta;
    }
}

fn main() {
    let mut rect = Rectangle { width: 10, height: 5 };
    println!("old area: {}", rect.area());
    rect.inc_width(5);
    println!("new area: {}", rect.area());
}
```