### 🔑 The main rule of input in Rust

**Rust always reads any keyboard input as text (string).** To work with the data further, we need a mutable `String` buffer and a chain of methods for clearing and transforming.

### 1. Shadowing and error handling (`match`)

In Rust, you can declare a new variable with the **same name** as the old one. This is called **shading** (Shadowing). This is ideal for converting string `guess` to numeric `guess` without having to come up with names like `guess_str` and `guess_num`.

#### Option A: Shadowing with kernel error (`expect`)

If the user enters letters, the program will crash with your error text (panic).



```Rust
let mut guess = String::new();
std::io::stdin().read_line(&mut guess).expect("Failed to read line");

// Declare a new variable guess of type u32, overriding the old line:
let guess: u32 = guess.trim().parse()
.expect("Please write a number!!!");
```

#### Option B: Shading with error handling (`match` from Chapter 2)

If the user enters letters, the program will not crash, but will ignore the error and ask for input again!



```Rust
loop {
    println!("Enter number:");

    let mut guess = String::new();
    std::io::stdin().read_line(&mut guess).expect("Read error");

    //Shading + match
    let guess: u32 = match guess.trim().parse() {
        Ok(num) => num, // Parsed? Great, guess will contain num, let's go to LINE 14!
        Err(_) => continue, // Error? Let's jump straight to LINE 2 (to the beginning of the cycle)!
    };

    println!("You entered a number: {guess}");
    // ... further comparison via match guess.cmp(&secret_number)
}

```
#### Option B: normal console input
```rust
use std::io;

fn main() {
let mut x = String::new();

io::stdin().read_line(&mut x)
.expect("Error reading line");

println!("You entered: {x}");
}
```
### 2. Conversion via Turbofish 🐟 (`::<Type>`)

Instead of explicitly specifying the type on the left (`let x: i32 = ...`), you can attach the type directly to the `.parse()` method using the turbo-fish syntax - `::<Type>`.



```Rust
use std::io;

fn main() {
let mut num1 = String::new();
let mut num2 = String::new();

println!("Enter 1st number:");
io::stdin().read_line(&mut num1).expect("Read error");

println!("Enter 2nd number:");
io::stdin().read_line(&mut num2).expect("Read error");

// TURBO FISH IN ACTION:
// There is NO NEED to write the type of the variable on the left, Rust will understand everything itself!
let num_int1 = num1.trim().parse::<i32>().unwrap();
let num_int2 = num2.trim().parse::<i32>().unwrap();

let result = num_int1 + num_int2;
println!("Sum: {result}");
}
```

#### 🛠 Analysis of chips:

- **`.parse::<i32>()`** - forcibly tells the `.parse()` method what type the string should be transformed into (`i32`, `u32`, `f64`, `bool`).

- **`.unwrap()`** - quick “hacking” of the result. Returns the value if everything is ok. If there is an error inside, the program crashes instantly.

- **`.expect("msg")`** - the same as `.unwrap()`, but when it crashes it displays your clear message.


### 3. Parsing Boolean type (`bool`) via Turbofish

Turbo fish perfectly parses Boolean values (`true` / `false`):



```Rust
use std::io;

fn main() {
    let mut input = String::new();
    println!("Enter true or false:");

    io::stdin().read_line(&mut input).expect("Read error");

    // Convert to lowercase and parse to bool using ::<bool>()
    let likes_rust = input.trim().to_lowercase().parse::<bool>().unwrap();

    match likes_rust {
        true => println!("Our man! 🦾 🦀"),
        false => println!("Nothing, you'll fall in love soon. 😉"),
    }
}
```

### 4. Type casting using `as` (Casting)

Rust never automatically folds different types (for example, `i16` and `u8`). They must be specified manually using the `as` operator.



```Rust
let num1: i16 = 15;
let num2: u8 = 15;

// Without `as i16` the compiler will throw a Mismatched Types error
let result: i16 = num1 + (num2 as i16);
```

#### ⚠️ Iron rule of casting:

**Always cast a smaller type to a larger one** (for example, from `u8` to `i16` or from `i32` to `f64`).

> **Important:** If you try to compress a larger type into a smaller one (for example, make `1000_i32 as u8`), Rust will not throw a compilation error, but the number will be truncated (overflow) and you will get buggy, distorted data.

### 🧠 Final cheat sheet-algorithm (Remember as Our Father)

1. **Buffer creation:** `let mut input = String::new();`

2. **Reading a line:** `io::stdin().read_line(&mut input).expect("...");`

3. **Clearing from Enter (`\n` / `\r\n`):** `input.trim()`

4. **Conversion (Option 1 - Shading with type on the left):** `let input: u32 = input.trim().parse().unwrap();`

5. **Conversion (Option 2 - Turbofish Style 🐟):** `let input = input.trim().parse::<u32>().unwrap();`

6. **Casting numbers:** Reduce through `as` only from bottom to top (`u8` ➔ `u16` ➔ `i32`).
