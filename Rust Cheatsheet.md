# Rust Cheat Sheet

## Create and Run Project

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

The base is denoted with a lower case character. Underscore \_ may be used to make long numbers more readable. The type may be specified explicitly, eg. `25u16`

| Type           | Example             |
| -------------- | ------------------- |
| Decimal        | `34678` or `34_678` |
| Hex            | `0xFF`              |
| Octal          | `0o77`              |
| Binary         | `0b1111_0000`       |
| Byte (u8 only) | `b'A' `             |

## Constants

Constants are also immutable, but they can have global scope. Their type must be declared explicity. The naming convention is to use all caps.

```rust
const SECONDS_PER_DAY: u32 = 60 * 60 * 24;    // Must use an explicit type
```

## Functions

Functions can appear in the code in any order. They do not need to be defined before use.

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
    User {
        name        // Shorthand avoids repeating the parameter "name: name"
        age: age    // or the verbose way
    }
}

user1 = init_user("tom", 22);
```

### Enums

An enum defines a custom type with a discrete set of variants.

```rust
// Define a enum type.
enum Fruit {        // Enum name is capitalized
    Apple,          // Variants are also capitalized
    Orange,
    Pear,
}

// Access an enum
let snack = Fruit::Orange;  // Orange is namespaced to Fruit
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

An if-else statement is an expression which returns the value of any expression in the matching arm.

```rust
    let x = if p > 0 {2*p} else {0}   // Similar to the ternary operator in C
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
let s1 = String::from("hello");   // s1 is a reference to the string on the heap
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

### References & Borrowing

```rust

```

### Mutable References

```rust

```

### Slices

```rust

```
