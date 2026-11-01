## Arrays (`array`) - How search, inversion and enumeration work without dumping code

And now to how to do “funny things” with arrays if they don’t have methods themselves. We use iterators and slices.

### To declare an array, you need to write let name = {and something inside}, so the cool thing is that you can set the type of the array yourself, like this:
```rust
fn main() {
// i32 is the type, and 10 is how many elements are in the array
let _nums: [i32; 10] = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
}
```

### Example 1, we ask the user what month it is and he displays what time of year:

```rust
use std::io;

fn main() {
    let seasons = ["Winter", "Spring", "Summer", "Autumn"]; // Array with seasons

    let mut user_month = String::new(); // variable with information entered by the user

    io::stdin()
        .read_line(&mut user_month) //read
        .expect("Failed to read line");

    let user_month = user_month.trim(); // Remove spaces

    match user_month.to_lowercase().as_str() {
        "january" | "december" | "february" => println!("{}", seasons[0]),
        "march" | "april" | "may" => println!("{}", seasons[1]),
        "june" | "july" | "august" => println!("{}", seasons[2]),
        "september" | "october" | "november" => println!("{}", seasons[3]),
        _ => println!("Unknown month."),
    }
}

```
## I use match user_month.to_lowercase().as_str() so that when the user enters the month with a small or capital letter, or in caps, it doesn’t screw him up



### Fun 1: Reversing (inverting) an array

The array itself cannot be flipped in place if it is immutable. But you can read it backwards.

Rust

```rust
fn main() {
    let numbers =;

    // `.iter()` makes an iterator from the array, and `.rev()` reverses the search direction
    for num in numbers.iter().rev() {
        println!("Number: {}", num); // Prints 5, 4, 3, 2, 1
    }
}

```

### Fun 2: Searching for an element (without LINQ and mess)

To find an element, we use the `.find()` iterator method. It returns a special type `Option` (can contain a value, or `None` if nothing is found) - this eliminates the error that the program will crash if there is no element.

Rust

```rust
fn main() {
    let engineering_codes =;

    // Look for the 404 code in the array
    let rough_code = 404;
    let found = engineering_codes.iter().find(|&&x| x == rough_code);

    match found {
        Some(code) => println!("Code {} found in the system!", code),
        None => println!("Code not found."),
    }
}

```

### Fun 3: Quickly filling with identical values

If you need a 100-element array filled with zeros or ones, you don't need a `for` loop. Rust has a special repetition syntax.

Rust

```rust
fn main() {
// Creates an array of 50 elements of type i32, and each element is -1
let background_buffer = [-1; 50];

println!("Array size: {}", background_buffer.len());
println!("First element: {}", background_buffer[0]);
}
```

### Fun 4: Outputting an array in one line (Debugging)

In C#, if you write `Console.WriteLine(array)`, you will simply get the name of the system type in the console (`System.Int32[]`). In Rust, the debugging macro outputs the struct structure "as is".

Rust

```rust
fn main() {
let matrix_row = [1.5, 2.3, 4.0];
// The `{:?}` flag forces the compiler to use built-in Debug output
println!("Array log: {:?}", matrix_row); // Outputs: Array log: [1.5, 2.3, 4.0]
}
```

### Summary: Why Rust is great in this regard

In Rust, everything is separated:

- The array is responsible **only for storing data**.

- Iterators are responsible **only for traversal and logic** (search, rotation, filtering).
