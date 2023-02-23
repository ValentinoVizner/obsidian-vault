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
An assignment will transfer ownership between variables:
```rust
fn main() {
    let s1: String = String::from("Hello!");
    let s2: String = s1;
    println!("s2: {s2}");
    // println!("s1: {s1}");
}
```
-   The assignment of `s1` to `s2` transfers ownership.
-   The data was _moved_ from `s1` and `s1` is no longer accessible.
-   When `s1` goes out of scope, nothing happens: it has no ownership.
-   When `s2` goes out of scope, the string data is freed.
-   There is always _exactly_ one variable binding which owns a value.

### Moved Strings in Rust
```rust
fn main() {
    let s1: String = String::from("Rust");
    let s2: String = s1;
}
```
-   The heap data from `s1` is reused for `s2`.
-   When `s1` goes out of scope, nothing happens (it has been moved from).
Before move to `s2`:
![[Screenshot 2023-02-19 at 19.49.44.png]]

### Moves in Function Calls
When you pass a value to a function, the value is assigned to the function parameter. This transfers ownership:
```rust
fn say_hello(name: String) {
    println!("Hello {name}")
}

fn main() {
    let name = String::from("Alice");
    say_hello(name);
    // say_hello(name); // returns error since name ownership moved to the function call before this one
}
```

### Copying and Cloning
While move semantics are the default, certain types are copied by default:
```rust
fn main() {
    let x = 42;
    let y = x;
    println!("x: {x}");
    println!("y: {y}");
}
```
These types implement the `Copy` trait.

You can opt-in your own types to use copy semantics:
```rust
#[derive(Copy, Clone, Debug)]
struct Point(i32, i32);

fn main() {
    let p1 = Point(3, 4);
    let p2 = p1;
    println!("p1: {p1:?}");
    println!("p2: {p2:?}");
}
```
-   After the assignment, both `p1` and `p2` own their own data.
-   We can also use `p1.clone()` to explicitly copy the data.
How do you know if a type **implements** copy? (implements = can use) You can check the documentation. For example, here is the documentation for char:

[https://doc.rust-lang.org/std/primitive.char.html](https://doc.rust-lang.org/std/primitive.char.html)

On the left you can see **Trait Implementations**. You can see for example **Copy**, **Debug**, and **Display**. So you know that a `char`:

-   is copied when you send it to a function (**Copy**)
-   can use `{}` to print (**Display**)
-   can use `{:?}` to print (**Debug**)
```rust
fn prints_number(number: i32) { // There is no -> so it's not returning anything
                             // If number was not copy type, it would take it
                             // and we couldn't use it again
    println!("{}", number);
}

fn main() {
    let my_number = 8;
    prints_number(my_number); // Prints 8. prints_number gets a copy of my_number
    prints_number(my_number); // Prints 8 again.
                              // No problem, because my_number is copy type!
}
```
But if you look at the documentation for String, it is not copy type.

[https://doc.rust-lang.org/std/string/struct.String.html](https://doc.rust-lang.org/std/string/struct.String.html)
On the left in **Trait Implementations** you can look in alphabetical order. A, B, C... there is no **Copy** in C. But there is **Clone**. **Clone** is similar to **Copy**, but usually needs more memory. Also, you have to call it with `.clone()` - it won't clone just by itself.

In this example, `prints_country()` prints the country name, a `String`. We want to print it two times, but we can't:
```rust
fn prints_country(country_name: String) {
    println!("{}", country_name);
}

fn main() {
    let country = String::from("Kiribati");
    prints_country(country);
    prints_country(country); // ⚠️
}

```
But now we understand the message:
```shell
error[E0382]: use of moved value: `country`
 --> src\main.rs:4:20
  |
2 |     let country = String::from("Kiribati");
  |         ------- move occurs because `country` has type `std::string::String`, which does not implement the `Copy` trait
3 |     prints_country(country);
  |                    ------- value moved here
4 |     prints_country(country);
  |                    ^^^^^^^ value used here after move
```
The important part is `which does not implement the Copy trait`. But in the documentation we saw that String implements the `Clone` trait. So we can add `.clone()` to our code. This creates a clone, and we send the clone to the function. Now `country` is still alive, so we can use it.
```rust
fn prints_country(country_name: String) {
    println!("{}", country_name);
}

fn main() {
    let country = String::from("Kiribati");
    prints_country(country.clone()); // make a clone and give it to the function. Only the clone goes in, and country is still alive
    prints_country(country);
	// prints_country(country); // but not anymore since we did not use .clone()
}
```
Of course, if the `String` is very large, `.clone()` can use a lot of memory. One `String` can be a whole book in length, and every time we call `.clone()` it will copy the book. So using `&` for a reference is faster, if you can. For example, this code pushes a `&str` onto a `String` and then makes a clone every time it gets used in a function:
```rust
fn get_length(input: String) { // Takes ownership of a String
    println!("It's {} words long.", input.split_whitespace().count()); // splits to count the number of words
}

fn main() {
    let mut my_string = String::new();
    for _ in 0..50 {
        my_string.push_str("Here are some more words "); // push the words on
        get_length(my_string.clone()); // gives it a clone every time
    }
}
```
That's 50 clones. Here it is using a reference instead, which is better:
```rust
fn get_length(input: &String) {
    println!("It's {} words long.", input.split_whitespace().count());
}

fn main() {
    let mut my_string = String::new();
    for _ in 0..50 {
        my_string.push_str("Here are some more words ");
        get_length(&my_string);
    }
}

```
Instead of 50 clones, it's zero.
### Borrowing
Instead of transferring ownership when calling a function, you can let a function _borrow_ the value:
```rust
#[derive(Debug)]
struct Point(i32, i32);

fn add(p1: &Point, p2: &Point) -> Point {
    Point(p1.0 + p2.0, p1.1 + p2.1)
}

fn main() {
    let p1 = Point(3, 4);
    let p2 = Point(10, 20);
    let p3 = add(&p1, &p2);
    let p4 = add(&p1, &p2);
    println!("{p1:?} + {p2:?} = {p3:?} or {p4:?}");
}
```


### Shared and Unique Borrows
Rust puts constraints on the ways you can borrow values:

-   You can have one or more `&T` values at any given time, _or_
-   You can have exactly one `&mut T` value.
```rust
fn main() {
    let mut a: i32 = 10;
    let b: &i32 = &a;

    {
        let c: &mut i32 = &mut a;
        *c = 20;
    }

    println!("a: {a}");
    println!("b: {b}");
}
```

### Lifetimes

A borrowed value has a _lifetime_:

-   The lifetime can be elided: `add(p1: &Point, p2: &Point) -> Point`.
-   Lifetimes can also be explicit: `&'a Point`, `&'document str`.
-   Read `&'a Point` as “a borrowed `Point` which is valid for at least the lifetime `a`”.
-   Lifetimes are always inferred by the compiler: you cannot assign a lifetime yourself.
    -   Lifetime annotations create constraints; the compiler verifies that there is a valid solution.

### Lifetimes in Function Calls

In addition to borrowing its arguments, a function can return a borrowed value:
```rust
#[derive(Debug)]
struct Point(i32, i32);

fn left_most<'a>(p1: &'a Point, p2: &'a Point) -> &'a Point {
    if p1.0 < p2.0 { p1 } else { p2 }
}

fn main() {
    let p1: Point = Point(10, 10);
    let p2: Point = Point(20, 20);
    let p3: &Point = left_most(&p1, &p2);
    println!("left-most point: {:?}", p3);
}
```
-   `'a` is a generic parameter, it is inferred by the compiler.
-   Lifetimes start with `'` and `'a` is a typical default name.
-   Read `&'a Point` as “a borrowed `Point` which is valid for at least the lifetime `a`”.
    -   The _at least_ part is important when parameters are in different scopes.

### Lifetimes in Data Structures

If a data type stores borrowed data, it must be annotated with a lifetime:
```rust
#[derive(Debug)]
struct Highlight<'doc>(&'doc str);

fn erase(text: String) {
    println!("Bye {text}!");
}

fn main() {
    let text = String::from("The quick brown fox jumps over the lazy dog.");
    let fox = Highlight(&text[4..19]);
    let dog = Highlight(&text[35..43]);
    // erase(text);
    println!("{fox:?}");
    println!("{dog:?}");
}
```


## Exercises Part 2

### Designing a Library

We will learn much more about structs and the `Vec<T>` type tomorrow. For now, you just need to know part of its API:
```rust
fn main() {
    let mut vec = vec![10, 20];
	let b = vec![30, 40, 50];
	vec.extend(b);
    println!("middle value: {}", vec[vec.len() / 2]);
    for item in vec.iter() {
        println!("item: {item}");
    }
}
```

```rust
struct Library {
    books: Vec<Book>,
}

struct Book {
    title: String,
    year: u16,
}

impl Book {
    // This is a constructor, used below.
    fn new(title: &str, year: u16) -> Book {
        Book {
            title: String::from(title),
            year,
        }
    }
}

// This makes it possible to print Book values with {}.
impl std::fmt::Display for Book {
    fn fmt(&self, f: &mut std::fmt::Formatter<'_>) -> std::fmt::Result {
        write!(f, "{} ({})", self.title, self.year)
    }
}

impl Library {
    fn new() -> Library {
        unimplemented!()
    }

    //fn len(self) -> usize {
    //    unimplemented!()
    //}

    fn is_empty(&self) -> bool {
	    self.books.is_empty()
    }

    //fn add_book(self, book: Book) {
    //    unimplemented!()
    //}

    //fn print_books(self) {
    //    unimplemented!()
    //}

    //fn oldest_book(self) -> Option<&Book> {
    //    unimplemented!()
    //}
}

// This shows the desired behavior. Uncomment the code below and
// implement the missing methods. You will need to update the
// method signatures, including the "self" parameter! You may
// also need to update the variable bindings within main.
fn main() {
    let library = Library::new();

    println!("Our library is empty: {}", library.is_empty());
    //
    //library.add_book(Book::new("Lord of the Rings", 1954));
    //library.add_book(Book::new("Alice's Adventures in Wonderland", 1865));
    //
    //library.print_books();
    //
    //match library.oldest_book() {
    //    Some(book) => println!("My oldest book is {book}"),
    //    None => println!("My library is empty!"),
    //}
    //
    //println!("Our library has {} books", library.len());
}
```