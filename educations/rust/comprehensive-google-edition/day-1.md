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
    let mut array: [i8; 7] = [23; 7];
    array[5] = 6;
    println!("array: {:?}", array);
}
```

Tuple assignment and access:
```rust
fn main() {
    let tuple: (i8, bool) = (0, false);
    println!("1st index: {}", tuple.0);
    println!("2nd index: {}", tuple.1);
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

## Exercise 1: Implicit Conversions
Rust will not automatically apply _implicit conversions_ between types ([unlike C++](https://en.cppreference.com/w/cpp/language/implicit_conversion)). You can see this in a program like this
```rust
fn multiply(x: i8, y: i8) -> i8 {
    x * y
}

fn main() {
    let x: i16 = 15;
    let y: i8 = 1000;

    println!("{x} * {y} = {}", multiply(x.into(), y.into())); // without into, Rust will throw an error as types From and Into do not match
}
```
The Rust integer types all implement the [`From<T>`](https://doc.rust-lang.org/std/convert/trait.From.html) and [`Into<T>`](https://doc.rust-lang.org/std/convert/trait.Into.html) traits to let us convert between them. The `From<T>` trait has a single `from()` method and similarly, the `Into<T>` trait has a single `into()` method.

The standard library has an implementation of `From<i8> for i16`, which means that we can convert a variable `x` of type `i8` to an `i16` by calling `i16::from(x)`. Or, simpler, with `x.into()`, because `From<i8> for i16` implementation automatically create an implementation of `Into<i16> for i8`.

## Exercise 2: Arrays and `for` Loops
You can print an array by asking for its debug representation with `{:?}`:
```rust
fn main() {
    let array = [10, 20, 30];
    println!("array: {array:?}");
}
```

Rust lets you iterate over things like arrays and ranges using the `for` keyword:
```rust
fn main() {
    let array = [10, 20, 30];
    print!("Iterating over array:");
    for n in array {
        print!(" {n}");
    }
    println!();

    print!("Iterating over range:");
    for i in 0..3 {
        print!(" {}", array[i]);
    }
    println!();
}
```

Use the above to write a function `pretty_print` which pretty-print a matrix and a function `transpose` which will transpose a matrix (turn rows into columns).
```rust
// TODO: remove this when you're done with your implementation.
#![allow(unused_variables, dead_code)]

fn transpose(matrix: [[i32; 3]; 3]) -> [[i32; 3]; 3] {
    unimplemented!()
}

fn pretty_print(matrix: &[[i32; 3]; 3]) {
    unimplemented!()
}

fn main() {
    let matrix = [
        [101, 102, 103], // <-- the comment makes rustfmt add a newline
        [201, 202, 203],
        [301, 302, 303],
    ];

    println!("matrix:");
    pretty_print(&matrix);

    let transposed = transpose(matrix);
    println!("transposed:");
    pretty_print(&transposed);
}
```
Solution
```rust

fn transpose(matrix: [[i32; 3]; 3]) -> [[i32; 3]; 3] {
    let mut transposed_matrix = [[0; 3]; 3];
    for i in 0..3 {
        for j in 0..3 {
            transposed_matrix[j][i] = matrix[i][j];
        }
    }
    return transposed_matrix;
}

fn pretty_print(matrix: &[[i32; 3]; 3]) {
    for row in matrix {
        println!("{row:?}");
    }
}

fn main() {
    let matrix = [
        [101, 102, 103], // <-- the comment makes rustfmt add a newline
        [201, 202, 203],
        [301, 302, 303],
    ];

    println!("matrix:");
    pretty_print(&matrix);

    let transposed = transpose(matrix);
    println!("transposed:");
    pretty_print(&transposed);
}
```

## Variables
Rust provides type safety via static typing. Variable bindings are immutable by default:
```rust
fn main() {
    let x: i32 = 10;
    println!("x: {x}");
    x = 20;
    println!("x: {x}");
}
```

### Type Inference
Rust will look at how the variable is _used_ to determine the type:
```rust
fn takes_u32(x: u32) {
    println!("u32: {x}");
}

fn takes_i8(y: i8) {
    println!("i8: {y}");
}

fn main() {
    let x = 10;
    let y = 20;

    takes_u32(x);
    takes_i8(y);
    takes_u32(y);
}
```

### Static and Constant Variables
Global state is managed with static and constant variables.
#### const
```rust
const DIGEST_SIZE: usize = 3;
const ZERO: Option<u8> = Some(42);

fn compute_digest(text: &str) -> [u8; DIGEST_SIZE] {
    let mut digest = [ZERO.unwrap_or(0); DIGEST_SIZE];
    for (idx, &b) in text.as_bytes().iter().enumerate() {
        digest[idx % DIGEST_SIZE] = digest[idx % DIGEST_SIZE].wrapping_add(b);
    }
    digest
}

fn main() {
    let digest = compute_digest("Hello");
    println!("Digest: {digest:?}");
}
```

#### static
```rust
static BANNER: &str = "Welcome to RustOS 3.14";

fn main() {
    println!("{BANNER}");
}
```
This is useful for unsafe and embedded code, and the variable lives through the entirety of the program execution.

### Scopes and Shadowing
You can shadow variables, both those from outer scopes and variables from the same scope:
```rust
fn main() {
    let a = 10;
    println!("before: {a}");

    {
        let a = "hello";
        println!("inner scope: {a}");

        let a = true;
        println!("shadowed in inner scope: {a}");
    }

    println!("after: {a}");
}
```

## Memory Management
Traditionally, languages have fallen into two broad categories:

-   Full control via manual memory management: C, C++, Pascal, …
-   Full safety via automatic memory management at runtime: Java, Python, Go, Haskell, …

Rust offers a new mix, full control _and_ safety via compile time enforcement of correct memory management with an explicit ownership concept.

### The Stack vs The Heap

-   Stack: Continuous area of memory for local variables.
    
    -   Values have fixed sizes known at compile time.
    -   Extremely fast: just move a stack pointer.
    -   Easy to manage: follows function calls.
    -   Great memory locality.

-   Heap: Storage of values outside of function calls.
    
    -   Values have dynamic sizes determined at runtime.
    -   Slightly slower than the stack: some book-keeping needed.
    -   No guarantee of memory locality.

### Stack Memory
Creating a `String` puts fixed-sized data on the stack and dynamically sized data on the heap:
```rust
fn main() {
    let s1 = String::from("Hello");
}
```
![stack](/assets/Screenshot%202023-02-19%20at%2011.36.14.png)
### Manual Memory Management
You allocate and deallocate heap memory yourself.

If not done with care, this can lead to crashes, bugs, security vulnerabilities, and memory leaks.

### Scope-Based Memory Management
Constructors and destructors let you hook into the lifetime of an object.

By wrapping a pointer in an object, you can free memory when the object is destroyed. The compiler guarantees that this happens, even if an exception is raised.

This is often called _resource acquisition is initialization_ (RAII) and gives you smart pointers.

#### C++ Example
```c++
void say_hello(std::unique_ptr<Person> person) {
  std::cout << "Hello " << person->name << std::endl;
}

```
-   The `std::unique_ptr` object is allocated on the stack, and points to memory allocated on the heap.
-   At the end of `say_hello`, the `std::unique_ptr` destructor will run.
-   The destructor frees the `Person` object it points to.

Special move constructors are used when passing ownership to a function:
```c++
std::unique_ptr<Person> person = find_person("Carla");
say_hello(std::move(person));
```

### Automatic Memory Management
An alternative to manual and scope-based memory management is automatic memory management:

-   The programmer never allocates or deallocates memory explicitly.
-   A garbage collector finds unused memory and deallocates it for the programmer.

### Memory Management in Rust
Memory management in Rust is a mix:

-   Safe and correct like Java, but without a garbage collector.
-   Depending on which abstraction (or combination of abstractions) you choose, can be a single unique pointer, reference counted, or atomically reference counted.
-   Scope-based like C++, but the compiler enforces full adherence.
-   A Rust user can choose the right abstraction for the situation, some even have no cost at runtime like C.

It achieves this by modeling _ownership_ explicitly.

## Ownership
All variable bindings have a _scope_ where they are valid and it is an error to use a variable outside its scope:
```rust
struct Point(i32, i32);

fn main() {
    {
        let point = Point(3, 4);
        println!("x: {}", point.0);
    }
    println!("y: {}", point.1);
}
```
-   At the end of the scope, the variable is _dropped_ and the data is freed.
-   A destructor can run here to free up resources.
-   We say that the variable _owns_ the value.

### Move Semantics
