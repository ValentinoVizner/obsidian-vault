# String
A `[String](https://doc.rust-lang.org/std/string/struct.String.html)` is a type that does not implement the `Copy` trait.
Meaning this won't work:
```rust
fn main() {  
    let name = String::from("Rust");  
    do_something(name);  
    println!("{}", name);  
}  
  
fn do_something(name: String) {  
    println!("Hello, {}!", name);  
}
```
This is because we are trying to use (borrow, in the case of `println!`) the value `name` which has already been moved into `do_something` in Line 3.

You cannot use a value that has been moved. Before `name` can even reach `println!`, it was moved into `do_something` and went _out of scope_ at the end of the function.
We can fix this in 2 ways:
`cloning` (it costs):
```rust
fn main() {  
    let name = String::from("Rust");  
    let name_clone = name.clone();  
  
    do_something(name);  
    println!("{}", name_clone);  
}  
  
fn do_something(name: String) {  
    println!("Name: {}!", name);  
}
```
`references`
```rust
fn main() {  
    let name = String::from("Rust");  
    do_something(&name);  
    println!("{}", name);  
}  
  
fn do_something(name: &str) {  
    println!("Hello, {}!", name);  
}
```

# i32
Life is much easier for types that implement the `Copy` trait, like `[i32](https://doc.rust-lang.org/std/primitive.i32.html)`.
So this works:
```rust
fn main() {  
    let age: i32 = 25;  
    do_something(age);  
	println!("{}", age);  
}  
  
fn do_something(age: i32) {  
    println!("Hello, {}!", age);  
}
```

# Struct with String field
A type can implement `Copy` if all of its components implement `Copy` (see [When can my type be](https://doc.rust-lang.org/std/marker/trait.Copy.html#when-can-my-type-be-copy) [Copy](https://doc.rust-lang.org/std/marker/trait.Copy.html#when-can-my-type-be-copy)[?](https://doc.rust-lang.org/std/marker/trait.Copy.html#when-can-my-type-be-copy)).
In these listings, we are concerned with the `Movie` struct which consists of one `String` field which _doesn’t_ implement `Copy`. As a result, `Movie` _cannot_ implement `Copy`.
This won't work:
```rust
#[derive(Debug)]
struct Movie {
    title: String,
}

fn main() {
    let movie = Movie { 
        title: String::from("Rust")
    };
    do_something(movie);
    println!("Movie: {:?}", movie);
}

fn do_something(movie: Movie) {
    println!("Movie: {:?}!", movie);
}
```
We can fix that by:
`cloning` (it costs):
```rust
#[derive(Debug, Clone)]  
struct Movie {  
    title: String,  
}  
  
fn main() {  
    let movie = Movie {   
        title: String::from("Rust")  
    };  
    do_something(movie.clone());  
    println!("Movie: {:?}", movie);  
}  
  
fn do_something(movie: Movie) {  
    println!("Movie: {:?}!", movie);  
}
```
`borrowing`
```rust
#[derive(Debug)]  
struct Movie {  
    title: String,  
}  
  
fn main() {  
    let movie = Movie {   
        title: String::from("Rust")  
    };  
    do_something(&movie);  
    println!("Movie: {:?}", movie);  
}  
  
fn do_something(movie: &Movie) {  
    println!("Movie: {:?}!", movie);  
}
```

# Vec  type String
Like `String`, [Vec](https://doc.rust-lang.org/std/vec/struct.Vec.html#) are moved, because they don’t implement the `Copy` trait (see [here](https://doc.rust-lang.org/std/vec/struct.Vec.html#trait-implementations)). The same move semantics apply.

Vectors (and other collections for that matter) are worth talking about because there are so many semantics involved — the container itself, the elements, and the iterators.

We use `Vec` as an example here because it’s a very common data structure for collections. Other collections that also don’t implement the `Copy` trait include `HashMap` and `HashSet`. Move semantics for arrays, on the other hand, work similarly as structs in that they depend on the type of the item — but that’s for another day.

```rust
fn main() {  
    let names = vec![  
        String::from("John"),  
        String::from("Jane"),  
   ];  
    do_something(names);  
    println!("{:?}", names);  
}  
  
fn do_something(names: Vec<String>) {  
    println!("{:?}", names);  
}
```
We can fix by:
`borrowing`:
```rust
fn main() {  
    let names = vec![  
       String::from("John"),  
       String::from("Jane"),  
   ];  
    do_something(&names);  
    println!("{:?}", names);  
}  
  
fn do_something(names: &[String]) {  
    println!("{:?}", names);  
}
```
`cloneing` (Note that a vector can be cloned if its underlying type implements the `Clone` trait.):
```rust
fn main() {  
    let names = vec![  
       String::from("John"),  
       String::from("Jane"),  
  ];  
    do_something(names.clone());  
    println!("{:?}", names);  
}  
  
fn do_something(names: Vec<String>) {  
    println!("{:?}", names);  
}
```

Can we move only one element of the `Vec`, NOPE, this won't work:
```rust
fn main() {  
    let names = vec![  
       String::from("John"),  
       String::from("Jane"),  
   ];  
    let name: String = names[0];  
    println!("Hello, {}", name);  
}
```

But why?

See, if you move only one element out of a `Vec`, you leave the vector in an **invalid state** — the vector is not a collection of homogenous elements anymore

Implicitly moving out of a `Vec` is not allowed as it would **leave the vector in an invalid state** — one element is moved out, the others are not (see [this](https://stackoverflow.com/questions/27904864/what-does-cannot-move-out-of-index-of-mean) StackOverflow post). If I were to iterate this vector, I might access an invalid memory (the element that was moved out) 😱. Thanks for protecting us, Rust.

So what do we do?
We can borrow the value that we want by using the indexing operator.
```rust
fn main() {  
    let names = vec![  
       String::from("John"),  
       String::from("Jane"),  
   ];  
    let name: &str = &names[0];  
    println!("Hello, {}", name);  
}
```

We can also borrow the value using the `.get` method.
```rust
fn main() {  
    let names = vec![  
       String::from("John"),  
       String::from("Jane"),  
   ];  
    let name: &str =   
    names.get(0).unwrap();  
    println!("Hello, {}", name);  
}
```

We can borrow the value using the `.first()` or `.last()` method (if you want the first and last item, of course).
```rust
fn main() {  
    let names = vec![  
       String::from("John"),  
       String::from("Jane"),  
   ];  
    let name: &str =   
    names.first().unwrap();  
    println!("Hello, {}", name);  
}
```

But… what if I want to _own_ an element?
We use the `.into_iter()` method to own the individual elements. More on iterators in the next chapter. Here, we manage to own the first element after calling `.next()`:
```rust
fn main() {  
    let names = vec![  
       String::from("John"),  
       String::from("Jane"),  
   ];  
    let mut names_iter =   
    names.into_iter();  
    let name: String =   
    names_iter.next().unwrap();  
    println!("Hello, {}", name);  
}
```

This example works if you want to own the first element. Afaik, if you want to own the element at index _n_ (for some reason), you would call `.skip(n)` then call `.next()`.

For `Vec`, we can `.pop()` the last element and own the data (only if you want the last element of a vector, of course).
```rust
fn main() {  
    let mut names = vec![  
       String::from("John"),  
       String::from("Jane"),  
   ];  
    let name: String = names.pop()  
    .unwrap();  
    println!("Hello, {}", name);  
}
```

# Iterator

[Iterators](https://doc.rust-lang.org/std/iter/index.html) play an extremely important role when it comes to ownership of elements in a collection.

In these examples we will use `Vec<String>`, intentionally using `String` as the element (which does not implement the `Copy` trait) so that we can demonstrate its move semantics in a vector.

## **for-loop**
Once we set `println` into`for` loop, we encounter problems:
```rust
fn main() {  
    let names = vec![  
        String::from("John"),  
        String::from("Jane"),  
    ];  
    
    for name in names {  
        println!("{}", name);  
    }  
    
    println!("Names: {:?}", names);  
}
```

After adding `into_iter()`, we still encounter same problem, but we can borrow elements from `names` using `.iter()`:
```rust
fn main() {  
    let names = vec![  
        String::from("John"),  
        String::from("Jane"),  
    ];  
  
    for name in names.iter() {  
        println!("{}", name);  
    }  
  
    println!("Names: {:?}", names);  
}
```

We can use a slice (which implements `IntoIterator`).
```rust
fn main() {  
    let names = vec![  
        String::from("John"),  
        String::from("Jane"),  
    ];  
  
    for name in &names {  
        println!("{}", name);  
    }  
  
    println!("Names: {:?}", names);  
}
```

## **Functional programming**

Using functional programming idioms makes it more obvious that iterators are involved when you want to iterate over elements (especially if you come from programming languages where you don’t have to deal with iterators directly).

Unlike for-loops, there are no implicit `.into_iter()` calls. As such, for vectors, we always need to call `.iter()`, `.into_iter()`, etc. to get an iterator.
This won't work without `.iter()`:
```rust
fn main() {  
    let names = vec![  
        String::from("John"),  
        String::from("Jane"),  
    ];  
      
    names.for_each(|name|  
       println!("{}", name));  
      
    println!("{:?}", names);  
}
```

And this will work:
```rust
fn main() {  
    let names = vec![  
        String::from("John"),  
        String::from("Jane"),  
    ];  
      
    names  
        .iter()  
        .for_each(|name|  
           println!("{}", name));  
      
    println!("{:?}", names);  
}
```

If you want to move the elements, use `.into_iter()`. However, note that we can’t use the vector afterwards:
```rust
fn main() {  
    let names = vec![  
        String::from("John"),  
        String::from("Jane"),  
    ];  
    
    names  
        .into_iter()  
        .for_each(|name|  
           println!("{}", name));  
    
    println!("{:?}", names);   
}
```

We have to redesign program such that we don’t use the moved elements
```rust
fn main() {  
    let names = vec![  
        String::from("John"),  
        String::from("Jane"),  
    ];  
      
    println!("{:?}", names);   
  
    names  
        .into_iter()  
        .for_each(|name|  
           println!("{}", name));  
}
```

# &str
Shared references (`&T`) are also `Copy` (see [here](https://doc.rust-lang.org/std/marker/trait.Copy.html#when-can-my-type-be-copy)). Here is an example of a commonly used type, the string slice `&str`.
We can pass `name` to `do_something` in Lines 3 and 4. Note that we are copying the _reference._, like this:
```rust
fn main() {  
    let name: &'static str = "Rust";  
    do_something(name);  
    do_something(name);  
}  
  
fn do_something(name: &str) {  
    println!("Hello, {:?}!", name);  
}
```

We can also `.clone()` just before passing `name` to `do_something` is redundant.
```rust
fn main() {  
    let name: &'static str = "Rust";  
    do_something(name.clone());  
    do_something(name.clone());  
}  
  
fn do_something(name: &str) {  
    println!("Hello, {:?}!", name);  
}
```