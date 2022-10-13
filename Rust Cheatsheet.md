# Rust Cheat Sheet

## Cargo: Create and Run a Project

Cargo is Rust's build system and package manager. `cargo build` will invoke the Rust compiler, _rustc_

```rust
$ cargo new my_project      // Create a new binary project inside my_project directory
$ cargo new --lib my_lib    // ...or create a new library project inside my_lib directory
$ cd my_project               // change to the project directory
...

...
$ cargo build               // Compile and build executable
$ cargo build --release     // Compile and build executable to release
$ cargo run                 // Compile build and run
$ cargo check               // Check for compilation without executable
$ cargo install cargo-modules   // install cargo module analyzer
$ cargo modules generate tree --with-types   // Generate a module tree
$ cargo doc                 // Generate documentation in target/doc/{crate name}/all.html
```

## Comments

```rust
// This is a line comment.

/*
    Block comments also work, but should generally be avoided
*/
```

## Documentation Comments

````rust
/// This is an OUTER doc comment.
/// Outer doc comments go BEFORE (outside) the item being commented, such as structs and functions

//! This is an INNER doc comment.
//! Inner doc comments go AFTER (inside) the item being documented, and are typically used for the main page of a crate.

/// Doc comments support markdown, eg:
/// # Heading
/// `code inline`
/// ``` code block```
````

# Printing

## Print Marcros

```rust
print!();                // Print to io::stdout
println!();              // Print to io::stdout with newline
eprint!();              // Print to io::stderr
eprintln!();
let s: String = format!();  // Print to a string
```

## Format Strings

```rust
println!("Test {}", x);  // Must implement fmt::Display trait
println!("{:?}", x);     // Debug Print struct with {:?} format.
println!("{:b}", x);     // Binary: 1000101001
println!("{:o}", x);     // Octal: 207454
println!("{:x}", x);     // Hex (lowercase): 10f2c
println!("{:X}", x);     // Hex (uppercase): 10F2C
println!("{:.3}", x);    // Print x to 3 decimal places precision
println!("{1}, {0}", "A", "B"); // Positional arguments -> Bob, Alice
println!("{subject} {verb}", subject = "Alice", verb = "jumped");  // Named arguments

let z: f64 = 1.0;
println!("{z}");         // Print variables
println!("{number:>5}", number=3);   // Right align in width 5
println!("{number:0>5}", number=3);  // Pad left hand side with zeroes -> 00003
```

## Implementing the Debug Trait

```rust
#[derive(Debug)]    // auto-implement Debug trait for any type
struct MyStruct(i32) {};
```

## Implementing the Display trait

```rust
use std::fmt; // Import `fmt`

struct Point2D {
    x: f64,
    y: f64,
}

// implement `Display` for `Point2D`
impl fmt::Display for Point2D {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        // Customize so only `x` and `y` are denoted.
        write!(f, "x: {}, y: {}", self.x, self.y)
    }
}
```

<div style="page-break-after: always;"></div>

# Primitive Types

Rust is strongly typed. Types are determined at compile-time but may be set explicitly or implicitly.

## Scalar Types

| Type             | Type specifier                   | Note                                                                       |
| ---------------- | -------------------------------- | -------------------------------------------------------------------------- |
| Signed integer   | `i8, i16, i32, i64, i128, isize` | Default integer is `i32`. Type `isize` is architecture defined signed int. |
| Unsigned integer | `u8, u16, u32, u64, u128, usize` | Type `usize` is architecture defined uint.                                 |
| Float            | `f32, f64`                       | default is `f64`                                                           |
| Boolean          | `bool`                           | `true` or `false`                                                          |
| Character        | `char`                           | A 4-byte unicode character eg. `let c = 'z'`;                              |
| Unit Type        | `() `                            | value = `()` empty tuple                                                   |

## Literals

The base is denoted with a lower case character.  
Underscore \_ in a literal is ignored. It improves readability.  
The type of a literal may be specified explicitly, eg. `25u16`

| Type           | Example                |
| -------------- | ---------------------- |
| Float          | `2.0`, `2f64`, `2_f64` |
| Decimal        | `34678` or `34_678`    |
| Hex            | `0xFF`                 |
| Octal          | `0o77`                 |
| Binary         | `0b1111_0000`          |
| Byte (u8 only) | `b'A' `                |

## Type Aliases: `type`

Define an alias for a type using the `type` keyword . This is useful for shortening long type names.  
The new type is just an alias and not a distinct type

```rust
type NanoSecond = u64;
let x: NanoSecond = 45;
```

## Tuples

Create tuple:

```rust
let a_tuple = (500, 6.4, 1);                  // with types set implicitly
let b_tuple: (i32, f64, u8) = (400, 3.4, 1);  // with types set explicitly
```

Access elements of tuple:

```rust
let (x, y, z) = a_tuple;       // By destructuring
let q = a_tuple.1;             // Using dot notation.  Zero-based
```

## Arrays

Arrays are fixed-length at run-time and contain only one type.

```rust
let a = [1, 2, 3, 4, 5];              // Create an array
let a: [i32; 5] = [1, 2, 3, 4, 5];    // Set type and length explicitly
let a = [3; 5];                       // Initialize array of length 5 with 3 in every position.

y = a[2];                             // Access the elements of an array with square brackets
let n = a.len();                      // Get the length of an array

calc(&a[1..4]);                       // Borrow (reference) a slice of the array from element 1 to element 3
```

# Globals: `const` and `static`

## Const

- Constants are immutable.
- Can be declared in any scope, including global.
- Type must be declared explicity. The naming convention is to use all caps.
- The const is inlined in the code with the value at compile time. It has no address.

```rust
const SECONDS_PER_DAY: u32 = 60 * 60 * 24;    // Must use an explicit type
```

## Static

- Statics are similar to consts but are actual variables. They can also be mutable, but it is unsafe.
- Typically used for mutable global variables
- Have `'static` lifetime implicitly

```rust
static THRESHOLD: u32 = 101;    // Must use an explicit type
```

# Custom Types

## Struct

```rust
struct User {         // Define a struct.  Upper camel case
    name: String,     // Define fields and their types.  Use snake_case
    age: i32,
}

let mut user1 = User {               // Instantiate a  struct
    name: String::from("John"),
    age: 35,
};

user1.name = String::from("bob");   // Access fields of a struct
```

A function that instantiates a struct using shorthand syntax and returns the new struct

```rust
fn init_user(name: String, age: i32) -> User {
  User {      // Final expression in the function returns User
    name        // Shorthand avoids repeating the parameter "name: name"
    age: age    // or the verbose way
  }
}

user1 = init_user("tom", 22);
```

Struct update syntax creates a new instance from an existing struct. But any fields stored on the heap will be _moved_ from user1, making user1 unuseable.

```rust
let user2 = User {
  name: String::from("alice"),   // Change the name field
  ..user1                        // ...but copy all other fields from user1
};
```

## Tuple Struct

Tuple structs are essentally named tuples.

```rust
struct Pair(i32, f32);              // Define a tuple struct type
let pair = Pair(1, 0.1);            // Instantiate a tuple struct
let Pair(integer, decimal) = pair;  // Destructure a tuple struct
let x = pair.0;                     // Access individual tuple struct elements
```

## Enum

Defines a type by enumerating its possible variants

```rust
enum Coin {     // Define enum
    Penny,
    Nickle,
    Dime,
    Quarter,
}

let coin = Coin::Nickle;        // Instantiate
let i = Coin::Nickle as i32;    // -> 1  Casting a C-like enum to an integer returns this index of the variant
```

Enums can associate data with each variant. Each variant can hold data of different types:

```rust
enum Message {  // Define enum
    Quit,                       // No accociated data
    Move { x: i32, y: i32 },    // Struct-like with named fields
    Write(String),
    ChangeColor(i32, i32, i32), // Tuple-like with ordered fields
}

let message = Message:ChangeColor(34, 56, 78);  // Instantiate
```

# Type Conversion

## Casting: `as`

_Primitive_ types may be explicitly cast using the `as` keyword.  
When casting from float to int the `as` keyword performs a _saturating_ cast.

```rust
let d = 200 as f64    // d will be f64
```

## Traits `From` and `Into`

For custom types, one must implement the `From` trait.  
The `Into` trait is the inverse, and is available for free  
Many types such as strings already have these traits already implemented.  
For cases where the conversion could fail, `TryFrom` and `TryInto` traits return a `Result`

```rust
use std::convert::From;

#[derive(Debug)]
struct Number {     // A custom type
    value: i32,
}

impl From<i32> for Number {     // Implement From
    fn from(item: i32) -> Self {
        Number { value: item }
    }
}

fn main() {
    let j = 30;
    let number1 = Number::from(j);     // Convert from i32 to Number

    let k = 5;
    let number2:Number = k.into(); // Convert k:i32 into a Number.  Type is required on the LHS
}

```

## Converting to a String: `.to_string()`

To convert a custom type to a String, simply implement the `fmt::Display` trait described oin the print! section.  
This will automatically also provide a .to_string() function

## Converting from a String: `.parse()`

To convert a string to a number, use `.parse`, which is available for types that implement the `FromStr` trait.

```rust
let parsed: i32 = "5".parse().unwrap();
let turbo_parsed = "10".parse::<i32>().unwrap();    // Alternate 'turbofish' syntax
```

# Variable Binding

- Variables should be initialized when they are declared (though its not absolutely required)
- Variables are block-scoped {...}.
- Variables are _immutable_ by default unless the `mut` keyword is used

```rust
let x = 5;            // Decalre and initialize an immutable variable.
let mut y = 10;       // mut keyword makes variable mutable
y = 7;                    // assign a new value to the variable
let user_age = 37;     // Use snake_case for variables
let z: i32 = 65;       // Explicitly set variable type with variable_name: type
```

## Shadowing

Variables can be re-declared or 'shadowed' using the `let` keyword. This can change the type, value, and mutability of a variable within the scope of a block.

```rust
fn main() {
  let x = 1;    // Initial binding of immutable variable
  {
    let x = "abc"; // This binding *shadows* the first, within the scope of this block
  }
  // x = 1 binding applies here, in the scope of the outer block
  let x = 2; // This binding *shadows* x=1 in the scope of the outer block.
}
```

<div style="page-break-after: always;"></div>

# Flow control

## If-Else

No parentheses are required around the conditional, but braces are required around each block.

```rust
if n < 0 {
    // Do something
} else if n > 0 {
    // Do something
} else {
    // Do something
}
```

An if-else statement is an _expression_ which returns the value of any expression in the matching arm.

```rust
let x = if p > 0 {2*p} else {0}   // Similar to the ternary operator in C
```

## Match

A `match` expression compares a value against a series of patterns and then executes code based on which pattern matches.

- Matches are exhaustive. Every case must be handled explicitly
- The first arm to match is the one that gets executed
- Variables can be used to match a pattern. A match will bind the value to that variable
- The \_ catchall can be used to disregard all other cases
- Matches are expressions which can return a value

```rust
#[derive(Debug)] // so we can inspect the state below
enum UsState {    // Enum of US States
    Alabama,
    Alaska,
    // --snip--
}

enum Coin {       // Enum of Coin type
    Penny,
    Nickel,
    Dime,
    Quarter(UsState),   // Quarters have an associated UsState enum attached.
}

let my_coin = Coin::Quarter(UsState::Alaska);    // Define a coin

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => {        // Block in braces is executed
          println!("Lucky penny!"); // Print something
          1                         // Returns a value of 1
        },
        Coin::Nickel => 5,          // Returns a value of 5
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}!", state);  // Extracts the value of UsState
            25                      // and returns 25
        },
    }
}
```

### Matching Option\<T>

```rust
fn plus_one(x: Option<i32>) -> Option<i32> {    // Function takes and returns Option<i32>
    match x {
        None => None,             // Handle the case of None
        Some(i) => Some(i + 1),   // Handle the case of Some()
    }
}

let x = plus_one(Some(5));    // x = Some(6)
let x = plus_one(None);       // x = None
```

## if-let

For simple cases where there is either a single match or not, the `if-let` expression can be used. It does what `match` does but slightly less verbose

```rust
if let Coin::Quarter(state) = coin {    // Try to match coin with Coin::Quarter(state)...
    println!("State quarter from {:?}!", state);    // Success, bind state
} else {
    count += 1;   // otherwise
}
```

`if let` is useful for 'unwrapping' Option\<T>

```rust
let result = Some(567_i32);     // Result is an Option<i32>
if let Some(value) = result {   // If it matches Some, bind value
    value   // Return value
}   // Otherwise return ()
```

## Destructuring in match

A match block can destructure tuples, arrays, slices, enums and structs

```rust
// Tuple destructuring
my_tuple  = (0, -2, 3);
match my_tuple {
    (0, y, z) => //match 0, bind y, z,
    (1, ..)  => // match 1 in first position, ignore the rest,
    (.., 2)  => // match 2 in last position, ignore the rest
    (3, .., 4)  =>  // match first and last position, ignore others
    ...
}

// Array destructuring is similar
let a = [1, -2, 6];
match a {
    [0, x1, x2] =>   // Match a[0]==0, bind the others
    [1, _, x2] => // match a[0]==1, ignore a[1], bind a[2]
    [-1, x1, ..] => match a[0]==-1, bind a[1], ignore the rest
    ...
}

// Struct destructuring is similar
struct Foo { x: (u32, u32), y: u32, }
let foo = Foo { x: (1, 2), y: 3 };
match foo {
    Foo { x: (1, b), y } => //match the 1, bind, b, y
    ...
}

```

## Infinite loop

`loop` continues forever until a `break` statement is encountered. The `continue` statement can be used to skip the rest of the iteration and start a new one.

```rust
loop {
  // Do stuff
  if n == 5 {
    break;      // Exit loop
  }
  if n == 10 {
    continue;   // Go to next iteration
  }
}
```

A loop can have a return value given by the value of the expression aftfollowing the `break` statement

```rust
let result = loop {
  // Do stuff
  if n == 5 {
    break 2*n;      // Exit loop and return 2*n = 10
  }
}
```

## While loop

```rust
while n < 100 {
  // Do stuff
}
```

## For-in loop

The for in loop takes an iterator.  
For numerical iterators, use a _range_: such as 1..100

```rust
for n in 1..100 {   // n = 1 to 99.  Upper limit is non-inclusive
  // Do stuff
}

for n in 1..=100 {   // n = 1 to 100.  Upper limit is inclusive
  // Do stuff
}
```

In general `for in` loops take an iterator on a collection:

```rust
// Borrow each element leaving collection untouched and available for reuse after the loop.
for name in names.iter() {...}

// Consume the collection. No longer available as it has been 'moved' within the loop.
for name in names.into_iter() {...}

// Mutably borrow each element, allowing for the collection to be modified in place
for name in names.iter_mut() {...}
```

## Functions

- Function bodies are made up of a series of _statements_, optionally ending in an _expression_.
- Functions can appear in the code in any order. They do not need to be defined before use.

```rust
fn main() {                // Code entry point is always the main() function
  ...
}

fn calculate_mass() {       // Names are snake_case
  ...
}

fn area(l: f64, w: f64) {  // Parameters with explicit parameter types
  ...
}

// Return value
fn area(l: f64, w: f64) -> f64 {  // Return type specified after the arrow ->
  l*w                // Returns value of the last expression, WITH NO SEMICOLON
}
```

## Methods & Associated Functions

Associated Functions are functions defined in the context of a struct, enum, or trait.

- Associated Functions are defined inside an implementation block with keyword `impl`
- Multiple implementation blocks are allowed

### Methods

A _method_ is an associated funtion where its first parameter is `&self` or `&mut self`.

- Methods are associated with an _instance_ of a struct, enum or trait.
- Methods are called using `struct_instance.method()`. The self parameter is passed implicitly

```rust
struct Rectangle {    // Define a struct
    width: u32,
    height: u32,
}

impl Rectangle {      // Define implementation block
    fn area(&self) -> u32 { // Define methods.  &self is always the first argument
        self.width * self.height
    }
}

let a = rect1.area(); // Call the method with dot notation.  &self is passed imlicitly
```

- Associated functions that _do not_ have `&self` as the first parameter are associated with the struct _type_ rather than an _instance_.

- These functions are often used for constructors or helper functions. They are accessed with the namespace operator `struct_type::function()`

```rust
impl Rectangle {
    fn square(size: u32) -> Rectangle {   // &self not passed in...not a method
        Rectangle {
            width: size,
            height: size,
        }
    }
}

let sq = Rectangle::square(3);  // Call function namespaced to Rectangle
```

The `match` flow control operator (below) is used for switching flow based on the value of an enum and its associated data, as well as for extracting associated data from an enum.

### Option\<T> Enum

The Option type is an enum that encodes when result could be something or nothing. The Option namespace is brought in in the rust prelude.

```rust
enum Option<T> {
    None,
    Some(T),
}
```

```rust
// Use an Option type
let x = Some(val);
let y = None();

// Test an Option type
x.is_some()     // Returns boolean
x.is_none()     // Returns boolean
```

## Ownership

### Rules

- Each value in Rust has a variable that’s called its owner.
- There can only be one owner at a time.
- When the owner goes out of scope, the value will be dropped.
- Assigning a value to another variable moves it. When a variable that includes data on the heap goes out of scope, the value will be cleaned up by drop unless the data has been moved to be owned by another variable

### Assigning Variables

For primitive variables of fixed size on the stack:

```rust
let x = 3;    // 3 is pushed onto the stack, owned by x
let y = x;    // Another 3 is separately pushed on the stack, owned by y
```

For larger values of unknown size, like strings, stored on the heap:

```rust
let s1 = String::from("hello");   // s1 holds a reference to the string on the heap
let s2 = s1;                      // s1 is 'moved' (shallow copied) to s2.  s2 now owns the string and s1 is invalidated.
let s3 = s2.clone();              // clone() explicitly makes a deep copy.  Both s2 and s3 are valid
```

### Function Parameters

Passing a variable to a function is similar to assigning it, as above

```rust
let x = 5;            // Stack variable...
makes_copy(x);        // x is passed by value on the stack into the function
println!("{}",x);     // x is still valid after the function

let s = String::from("hello");  // Heap variable...
takes_ownership(s);             // s's value moves into the function...
                                // datastore backing s is dropped when function goes out of scope
println!("{}",s);               // ERROR! s is no longer valid here

```

### Function Return Values

```rust
let s1 = String::from("hello");
let s2 = a_function(s1);  // s1 is moved into the function.  s1 is no longer valid.
                          // The function moves its return value into s2

fn a_function(a_string: String) -> String {
    a_string  // a_string is returned and moves out to the calling function
}
```

<div style="page-break-after: always;"></div>

## References & Borrowing

- A reference allows you to refer to a value (borrow it) without taking ownership.
- A reference’s scope starts from where it is introduced and continues through the last time it is used.
- The `&` operator denotes a reference:

```rust
let s = String::from("hello");
let len = calc_len(&s);            // Pass reference to String to function.  s is borrowed.

fn calc_len(s: &String) -> usize {  // Define function that takes a reference to a String
    s.len()
}
```

### Mutable References

- `&mut` denotes a mutable reference.
- At any given time, you can have _only one mutable reference_ or any number of immutable references.

```rust
let mut s = String::from("hello");   // A mutable String
change(&mut s);                      // &s is an mutable reference to s1

fn change(s: &mut String) {   // Defines a function that takes a mutable reference to a String
  s.push_str(", world");    // Mutate the String
}
```

### Slices

- A _slice_ is a _reference to part of a collection_ such as a string, array, or vector.
- It includes a pointer to the first element and a length.
- It is specified using a _range_ in brackets after the reference to the collection: `&a[x..y]`:

```rust
let a = [1, 2, 3, 4, 5, 6, 7, 8, 9];
let slice = &a[1..3];   // slice = &[2,3]
let x = &a[..5];      // Elements up to 4
let x = &a[6..]       // Elements 6 to end
let x = &a[..]        // Whole string
```

- A _string slice_ is a reference to a substring.
- A string slice has its own special type: `&str`

```rust
let s = String::from("hello world");
let x = &s[6..11];    // x is a string slice.  x = "world".  Characters 6-10
```

- String literals _are_ string slices
- A reference to a `String` can be passed directly into a function that takes an `&str`. It will be type-coerced.
- Try to define string manipulation functions to operate on type `&str`, for maximum generality

```rust
fn strfun(s: &str) -> &str {   // Define string functions to use type &str
  // ...
}

let s = String::from("hello world");  // String
let lit = "hi there";                 // String literal

let x = strfun(&s[3..5]);    // Can call the function with a slice, &str
let x = strfun(&s);          // ...or a reference to a String
let x = strfun(lit);         // ...or a literal
```

<div style="page-break-after: always;"></div>

## Rust Module System

### Package

A package is one or more crates that provide a set of functionality. A package contains a Cargo.toml file that defines the package and describes how to build those crates.

```
my_project      <-- Package named 'my_project'
│
├───Cargo.toml      <-- .toml file
├───Cargo.lock
├───src/
    │
    ├───main.rs     <-- binary crate called 'my_project' (root)
    ├───lib.rs      <-- library crate called 'my_project' (root)
    ├───sub_module.rs
    └───sub_module/
        │
        └───sub_sub_module.rs
```

### Crates

Crates contain a tree of modules that produces a library or executable. The top-most module in a crate is called 'crate'

```
crate
 └── top_module
     ├── submodule1
     │   ├── function1
     │   └── function2
     └── submodule2
         ├── function3
         ├── function4
         └── function5
```

### Modules

Modules organize code scope into different namespaces. Modules are defined in the code using the `mod` keyword, folllowed by the module name and a code block in braces.

```rust
mod top_module {
    mod submodule1 {            // Private module
        ... // functions, structs, enums etc
    }
    pub mod submodule2 {        // Public module
        ... // functions, structs, enums etc
    }
}
```

### Modules Across Multiple Files

To separate modules into different files, use a semicolon after the module name, rather than a block. This tells Rust to load the contents of the module from another file under '/src' having the same name as the module.

```rust
pub mod my_module;      // Load module 'my_module' from 'src/my_module.rs'
```

### Paths

Paths are way of naming an item, such as a struct, function, or module

- An _absolute_ path starts from a crate root by using its _crate name_ or the literal `crate`.
- A _relative_ path starts from the current module and uses `self`, `super`, or an identifier in the current module.

```rust
crate::main_module::submodule1::function1();        // Absolute path referring to the current crate

main_module::submodule1::function1();               // Relative path

super::submodule1::function1();                     // Refer up one level

self::submodule1::function1();                      // Refer to the current level
```

### Privacy

- All items (functions, methods, structs, enums, modules, and constants) are private by default.
- Items in a parent module can’t use private items inside child modules.
- Items in child modules _can_ use private items in their ancestor modules and their sibling modules

### Make Items Public with `pub`

- Use the `pub` keyword to mark items public.
- Making a module public doesn’t make its contents public. Use `pub` on the contents if needed.
- Individual elements of a struct must be made public for them to be visible

### Bring Items Into Scope with `use`

```rust
use rand::Rng;                  // Bring external dependencies defined in Cargo.toml into scope
use main::MyType as MyAlias;    // Create an alias to prevent namespace conflicts using 'as' keyword
use main::{self, sub1::sub_sub, sub2};   // Nested paths make 'use' statements more concise
use main::sub::*;               // Glob operator (*) brings everything in scope
pub use crate::main::sub;       // Re-export a name with 'pub use', making available to others
```

<div style="page-break-after: always;"></div>

## Collections

### Vector

```rust
// Initializing:
let v: Vec<i32> = Vec::new();    // Create a new vector with explicitly typed elements
let v = Vec::from([1, 2, 3, 4]); // Create a new vector from a literal
let v = vec![1, 2, 3, 4];           // Create a new vector from literals with inferred types, using vec! macro

// Accessing:
let x = v[3];                    // Get a reference to element 3.  Panic if out of range
let o = v.get(3);                // Returns an Option with a reference to an element, or None() if out of bounds
let o = v.get(1..2);             // Returns an Option with a reference to a slice, or None() if out of bounds
let o = v.get(1..2);             // Returns an Option with a reference to a slice, or None() if out of bounds

// Mutating:
v.push(5);                       // Push an element on the end of a vector
let an_option = v.pop();         // Pops last element and returns an Option to the value popped
```

### String

- Strings are UTF-8 encoded. Individual unicode scalars may correspond to multiple bytes in memory.
- Rust does not allow you to index into a string using []

```rust
// Create new strings
let mut s = String::new();                // Create a new empty String
let s = "A String Literal".to_string();   // Convert a string literal to a String
let s = String::from("A String Literal"); //  Create a String from a string literal

// Modify strings
s.push_str(&str);        // Append &str to s
s.push('c');             // Append a single character to s
let s = s1 + &s2;       // Concatenate s2 onto s1.  s3 now owns s1
let s = format!("{}-{}-{}", s1, s2, s3);  // Or use the format! macro and a template to concatenate

// Get length in bytes
let n = s.len();      // Bytes, not UTF-8 scalars

// Iterating over strings
for c in s.chars() {    // s.chars is an iterator over the utf-8 scalars
    ...
}

for b in s.bytes() {    // s.bytes() is an iterator over the bytes
    ...
}
```

### HashMap

- Hash Maps store a collection of key-value pairs. Hash Maps are not part of the rust prelude.

```rust
use std::collections::HashMap;    // Bring into scope

let mut hm = HashMap::new();            // Create a new mutable hash map
hm.insert(key, value);                  // Insert a key-value pair
let value = hm.get(&key);               // Returns an Option holding the value corresponding to the key, or None().
```

If a key already exists in the hash map, `insert` will overwrite it. To only insert an entry if it does not already exist, use the following:

```rust
hm.entry(key).or_insert(value);   // Returns a mutable reference to the existing value, if it exists, otherwise inserts the new one
```

<div style="page-break-after: always;"></div>

## Error Handling

When Rust encounters a non-recoverable error, it is said to 'panic'. You can call the macro `panic!` to halt the program and unwind the stack.

```rust
panic!("My error message");
```

Functions that can have an error should return a _Result_ enum which encapsulates both a successful result and an error. Result enums are brought into scope by the Rust prelude, so `Ok()` and `Err()` can be used directly without writing `Result::Ok()` or `Result::Err()` namespacing

```rust
enum Result<T, E> {
    Ok(T),      // No error, Ok contains the value
    Err(E),     // Error, Err contains an error type
}
```

### Propagate Errors Up

To propagate errors up to the caller function, return a `Result<T,E>`. Use `match` or `if` statements to handle the two cases and return Ok() or Err() accordingly:

```rust
fn caller_function() -> Result<My_Ok_Type, My_Error_Type> {
    //...snip
    if has_error return Err(e);   // e: My_Error_Type
    Ok(val)                      // Otherwise return Ok(). val: My_Ok_Type
}
```

Alternately, use `match`

```rust
fn caller_function() -> Result<T,E> {
    let x = match called_function() {
        Ok(val) => val,             // If Ok(), unwrap the value
        Err(e) => return Err(e),    // If Err(), return immediately with Err()
    };
    //... use x here...
}
```

Or, append the `?` operator to a Result. If the Result is Err, the error will be cast to the error type of the _caller_ function which will immediately return the Err() in _its_ Result. Otherwise the Ok value will be unwrapped and execution will continue. The ? operator can also be chained: ` let x = fn1()?.fn2()?;`

```rust
fn caller_function() -> Result<T,E> {
  let x = called_function()?;   // Unwrap the Ok value, or force caller to return Err()
  // ... use x here...
}
```

### Panic-on-Error

To handle a Result by panicking, one can use `match`:

```rust
let result = fn_returns_result();   // A function returns a Result enum

let x = match result {
    Ok(v) => v,          // If no error -> return v, binding v to x
    Err(error) => panic!("Problem: {:?}", error),  // If error -> panic
};
```

Or use these shorthand methods:

```rust
let x = result.unwrap();      // Binds an Ok value to x or panicks if Err
    //... or ...
let x = result.expect("My error message");    // Same as unwrap, but includes a custom error message
```

### Test a Result

To check if a Result is an Err or Ok, for example inside an assertion, use:

```rust
my_result.is_ok()       // Returns boolean
my_result.is_err()      // Returns boolean
```

<div style="page-break-after: always;"></div>

## Traits

Traits define behavior shared between different types, similar to an _interface_. A trait is a collection of methods defined for an unknown type: `Self`. Note: either the trait or the type must be local to the implementation crate

```rust
// Define a trait
pub trait MyTrait {
    fn method1(&self) -> String;   // provide a method signature for each type
    fn method2(&self) {            // Optionally provide a default implementation
        ...
    }
}

// Define a type or use an existing type
pub struct MyStruct {
    ...
}

// Implement the trait for the type
impl MyTrait for MyStruct {
    fn method1(&self) -> String {       // Methods go here
        ...
    }
    fn method2(&self) {
        ...
    }
}

// Call the trait like any other method
my_struct.method2()
```

Traits can be function parameters:

```rust
pub fn my_function(item: &impl MyTrait) { ... }   // Take a reference to item that implements MyTrait

pub fn my_function<T: MyTrait>(item: &T) { ... }  // This 'trait-bound' syntax is equivalent, without the sugar
```

Functions can return a trait:

```rust
fn returns_trait() -> impl MyTrait { ... }
```

### Derive Attribute

The compiler can provide basic implementations for the following traits via the `#[derive(trait_name)]` attribute:

- `Eq, PartialEq, Ord, PartialOrd` comparison traits
- `Clone`, to create T from &T via a copy.
- `Copy`, to give a type 'copy semantics' instead of 'move semantics'.
- `Hash`, to compute a hash from &T.
- `Default`, to create an empty instance of a data type.
- `Debug`, to format a value using the {:?} formatter.

# Generics

Generics are used to create abstract, type-independent code for items like functions, structs, enums, and methods. If the first use of type X is preceeded by <X>, then X is generic.

```Rust
fn my_function<T, U> (param1: T, param2: U) ->  T {   // Generic function with different parameter types
  ...
}

struct Point<T> {     // Generic struct
    x: T,
    y: T,
}

impl<T> Point<T> {    // Generic implementation
    //  Functions inside the impl do NOT need <T> after the function name
    ...
}

impl Point<f32> {   // Implement an explicit (NON-generic) type
    ...
}

enum Result<T, E> {   // Generic enum
    Ok(T),
    Err(E),
}

trait MyTrait<T> {
    // Define a trait on the
    fn do_my_trait(self, x: T);
}
```

## Trait Bounds

The types over which a generic is defined can be restricted to those types that implement certain traits. These are _trait bounds_

```rust
// Restrict the generic type 'T' to types that implement traits Display and Debug
// Multiple bounds are spearated with a '+'
fn printer<T: Display + Debug>(t: T) {
    ...
}
```

## Lifetimes

- Every _reference_ in Rust has a lifetime, which is the scope for which that reference is valid.
- Most of the time, lifetimes are implicit and inferred, but sometimes they need to be made explicit for the _borrow checker_

- Generic lifetime parameters define the relationship between references so the borrow checker can perform its analysis

### Lifetime Annotation Syntax

```rust
&i32        // a reference
&'a i32     // a reference with an explicit lifetime of 'a
&'a mut i32 // a mutable reference with an explicit lifetime of 'a

// Function parameter lifetimes
fn my_function<'a>(x: &'a str, y: &'a str) -> &'a str {...}

// Struct lifetimes
struct My_Struct<'a> {
    part: &'a str,
}
```

## Closures

A closure is an anonymous function you can save in a variable or pass as an argument to other functions.

- Closures do not require explicit typing of parameters and return values since they can often be inferred
- Closures can capture their environment and access variables from the scope in which they’re defined.

```rust
let my_closure = |param1, param2| {     // Define a closure
    // do something here...
    result // return a value
};

my_closure(x, y)                       // Call the closure
```

## Iterators

An iterator allows you to perform some task on a sequence of items in turn.

- Iterators are lazy: they have no effect until you call methods that consume the iterator.

```rust
let v1 = vec![1, 2, 3];   // Define a vector

let mut v1_iter = v1.iter();  // Create a (mutable) iterator on v1
x = v1_iter.next()            // Returns Some(&val) or None
```

...... not finished.....

<div style="page-break-after: always;"></div>

## Code Testing

Tests have the following form:

```rust
use whatever    // Bring into scope anything needed in the test

#[test]         // Define this function as a test
fn test1() {    // Define a test function with descriptive test name
    ...
    assert_eq!(a,b);    // Test assertion.  If true, the test passes
}
```

Other assertion macros:

```rust
assert!(boolean);   // Assert the boolean is true
assert_eq!(a,b);    // Assert a==b
assert_ne!(a,b);    // Assert a!=b
assert_eq!(a,b,"Result was {}", a_variable);  // Include an error message and data.
```

Or test for an expected panic with annotation `#[should_panic]`:

```rust
#[test]         // Define test
#[should_panic] // Use should_panic directive to mark tests that are expected to panic
fn check_for_panic() {
    // -- snip
}
```

Or return a _Result_ enum from a test and fail if there is an Err():

```rust
#[test]
fn return_a_result() -> Result<(), String> {
    if 2 + 2 == 4 {
        Ok(())    // Will pass test
    } else {
        Err(String::from("two plus two does not equal four")) // WIll fail test
    }
}
```

### Unit Tests

- _Unit_ tests go in the same file as the code they are testing.
- They need the annotation `#[cfg(test)]` to tell the compiler to compile the tests only if it is running `cargo test`
- They also need to the main code brought in scope with `use super::*;`
- When rust creates a new library with `cargo new my_library --lib`, it will automatically place a template for _unit_ tests at the bottom of the source file

```rust
//-- Source code goes here...

#[cfg(test)]    // tells compiler to only compile this module if running 'cargo test'
mod tests {     // This is the test module
    use super::*;   // Bring library code above into scope of this module

    #[test]          // Tests go here...
    fn unit_test1() {
        ...
    }
}
```

### Integration Tests

- Put integration test files in a directory named _tests_, located in the project folder at the same level as the _src_ directory.
- There is no need for the annotation `#[cfg(test)]` or `use super::*;`

```rust
use whatever;   // Bring

#[test]          // Tests go here...
fn integration_test1() {
    ...
}
```

### Running Tests

- Run all tests with `cargo test`
- Print output will be suppressed on _successful_ tests, unless you run `cargo test -- --show-output`
- Run a test with a specific name with `cargo test my_test`
- Run tests on a single thread to prevent interactions `cargo test -- --test-threads=1`

## Common Compiler Attributes

- `#[allow(dead_code)]` - suppresses warnings about unused code
- `#[test]` - define a test
- `#[ignore = "not yet implemented"]` - ignore a test
- `#[should_panic(expected = "values don't match")]` - test is only passed if code actually panics
- `#[derive(PartialEq, Clone)]` - automatically derive traits
- `#[inline]` - suggests performing an inline expansion.
