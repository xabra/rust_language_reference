# Rust Cheat Sheet

## Create and Run Project

Cargo is Rust's build system and package manager. `cargo build` will invoke the Rust compiler, _rustc_

```rust
$ cargo new my_project      // Create a new project inside my_project directory
$ cd my_project               // change to the project directory
...
$ cargo build               // Compile and build executable
$ cargo build --release     // Compile and build executable to release
$ cargo run                 // Compile build and run
$ cargo check               // Check for compilation without executable
```

## Comments

```rust
// This is a line comment.

/*
    Block comments also work, but should generally be avoided
*/

/// Triple slashes are for documentation
```

## Variable Binding

- Variables should be initialized when they are declared (though its not absolutely required)
- Variables are block-scoped.
- Variables are immutable by default unless the `mut` keyword is used

```rust
let x = 5;            // Decalre and initialize an immutable variable.
let mut y = 10;       // mut keyword makes variable mutable
y = 7;                    // assign a new value to the variable
let user_age = 37;     // Use snake_case for variables
let z: i32 = 65;       // Explicitly set variable type with variable_name: type
```

### Shadowing

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

## Literals

The base is denoted with a lower case character. Underscore \_ is ignored and may be used to make long numbers more readable. The type may be specified explicitly, eg. `25u16`

| Type           | Example                |
| -------------- | ---------------------- |
| Float          | `2.0`, `2f64`, `2_f64` |
| Decimal        | `34678` or `34_678`    |
| Hex            | `0xFF`                 |
| Octal          | `0o77`                 |
| Binary         | `0b1111_0000`          |
| Byte (u8 only) | `b'A' `                |

## Constants

Constants are also immutable, but they can have global scope. Their type must be declared explicity. The naming convention is to use all caps.

```rust
const SECONDS_PER_DAY: u32 = 60 * 60 * 24;    // Must use an explicit type
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

## Types

Rust is strongly typed. Types are determined at compile-time but may be set explicitly or implicitly.

### Scalar Types

| Type             | Type specifier                   | Note                                                                       |
| ---------------- | -------------------------------- | -------------------------------------------------------------------------- |
| Signed integer   | `i8, i16, i32, i64, i128, isize` | Default integer is `i32`. Type `isize` is architecture defined signed int. |
| Unsigned integer | `u8, u16, u32, u64, u128, usize` | Type `usize` is architecture defined uint.                                 |
| Float            | `f32, f64`                       | default is `f64`                                                           |
| Boolean          | `bool`                           | `true` or `false`                                                          |
| Character        | `char`                           | A 4-byte unicode character eg. `let c = 'z'`;                              |
| Unit Type        | `() `                            | value = `()` empty tuple                                                   |

### Casting Types

Types may be explicitly cast using the `as` keyword

```rust
let d = 200 as f64    // d will be f64
```

When casting from float to int the `as` keyword performs a _saturating cast_. If the floating point value exceeds the upper bound or is less than the lower bound, the returned value will be equal to the bound crossed.

### Tuples

Create tuple:

```rust
let a_tuple = (500, 6.4, 1);                  // with types set implicitly
let b_tuple: (i32, f64, u8) = (400, 3.4, 1);  // with types set explicitly
```

Access elements of tuple:

```rust
let (x, y, z) = a_tuple;       // Access elements with destructuring
let q = a_tuple.1;             // or with dot notation
```

### Arrays

Arrays are fixed-length and contain only one type.

```rust
let a = [1, 2, 3, 4, 5];              // Create an array
let a: [i32; 5] = [1, 2, 3, 4, 5];    // Set type and length explicitly
let a = [3; 5];                       // Initialize array of length 5 with 3 in every position.

y = a[2];                             // Access the elements of an array with square brackets
let n = a.len();                      // Get the length of an array

calc(&a[1..4]);                       // Borrow (reference) a slice of the array from element 1 to element 3
```

### Structs

Define a struct type

```rust
struct User {         // Struct name is capitalized
    name: String,     // Define fields and their types
    age: i32,
}
```

Instantiate a struct

```rust
let mut user1 = User {
    name: String::from("John"),
    age: 35,
};
```

Access fields in a struct

```rust
user1.name = String::from("bob");   // Set an element
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

Struct update syntax creates a new instance from an existing struct. But any fileds stored on the heap will be _moved_ from user1, making user1 unuseable.

```rust
let user2 = User {
  name: String::from("alice"),   // Change the name field
  ..user1                        // ...but copy all other fields from user1
};
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

### Enums

An enum defines a custom type with a discrete set of variants.

```rust
// Define a Fruit enum type.
enum Fruit {        // Enum name is capitalized
  Apple,          // Variants are also capitalized
  Orange,
  Pear,
}

// Instantiate a  variable of type Fruit
let snack = Fruit::Orange;  // Orange has type Fruit, value Orange.  It is namespaced to Fruit
```

Each variant of an enum can have data of any type associated with it:

```rust
// Define a Message type and variants
enum Message {
    Quit,                         // No data
    Move { x: i32, y: i32 },      // key-value struct.  Note BRACES, not parens
    Write(String),                // String argument
    ChangeColor(i32, i32, i32),   // i32 arguments
}

//  Instantiate 3 Messages
let m1 = Message::Quit;
let m2 = Message::Move { x: 3, y: 6 };
let m3 = Message::Write(String::from("Wrote"));
```

The `match` flow control operator (below) is used for switching flow based on the value of an enum and its associated data, as well as for extracting associated data from an enum.

### Option\<T> Enum

The Option type is an enum that encodes the common scenario in which a value could be something or it could be nothing.

- Forces the programmer to explicitly handle the case where a value is None, in contrast to 'null'
- Option namespace is brought in in the rust prelude.
- Option<T> is a distinct type, for each type T

```rust
enum Option<T> {
    None,
    Some(T),
}
```

```rust
let x = Option::Some(i32);  // No need to use Option namespace explicitly..
let y = Some(i32);          // this works also
let z = Some(u8);           // y and z are different types. Option<i32> and Option<u8>, respectively
```

## Flow control

### If-Else

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

### Match

A `match` expression compares a value against a series of patterns and then executes code based on which pattern matches.

- Matches are exhaustive. Every case must be handled explicitly
- The first arm to match is the one that gets executed
- Variables can be used to match a pattern. A match will bind the value to that variable
- The \_ catchall can be used to disregard all other cases

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

### if let

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
let result = Some(567 i32);     // Result is an Option<i32>
if let Some(value) = result {   // If it matches Some, bind value
    value   // Return value
}   // Otherwise return ()
```

### Infinite loop

Loop continues forever until a `break` statement is encountered. The `continue` statement can be used to skip the rest of the iteration and start a new one.

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

A loop can have a return value given by the value of the expression after `break`

```rust
let result = loop {
  // Do stuff
  if n == 5 {
    break 2*n;      // Exit loop and return 2*n = 10
  }
}
```

### While loop

```rust
while n < 100 {
  // Do stuff
}
```

### For-in loop

```rust
for n in 1..100 {   // n = 1 to 99
  // Do stuff
}
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

## Module System

packages, crate, modules...

## Collections

### Vector

### String

### HashMap

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

### Propagate Errors

To propagate errors up to the caller function, return a `Result<T,E>`. Use `match` or `if` statements to handle the two cases and return Ok() or Err() accordingly:

```rust
fn my_function() -> Result<My_Ok_Type, My_Error_Type> {
    //...snip
    if has_error return Err(e);   // e: My_Error_Type
    Ok(val)                      // Otherwise return Ok(). val: My_Ok_Type
}
```

Alternately, use `match`

```rust
fn caller_function() -> Result<T,E> {
    let val = match called_function() {
        Ok(val) => val,             // If Ok(), unwrap the value
        Err(e) => return Err(e),    // If Err(), return immediately with Err()
    };
    //...do something with val here
}
```

### The ? Operator

The `?` operator is applied to a `Result`. It is a shortcut for propagating errors.

- If the Result is Ok, its value will be unwrapped and execution will continue
- If the Result is Err, the error will be cast to the error type of the caller, and the caller will immediately return the Err() in its Result.

```rust
fn caller_function() -> Result<T,E>{
  let val = called_function()?;   // ? operator unwraps the Ok value, or forces caller_function to return the Err()
  // Do something with val here...
}
```

The ? operator can also be chained: ` let x = fn1()?.fn2()?;`

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
... or ...
let x = result.expect("My error message");    // Same as unwrap, but includes a custom error message
```

## Generics

## Code Testing
