### 1. Declaration and simple example

- **Keyword:** `fn`

- **Naming:** `snake_case` (all letters are small, words are underlined).

- **Parameter types:** For each incoming argument it is **required** to explicitly write the type.




```Rust
// A simple clear example: the function counts damage
fn deal_damage(health: i32, damage: i32) -> i32 {
health - damage // Return the remaining HP (without the semicolon at the end!)
}

fn main() {
let current_hp = deal_damage(100, 25);
println!("HP remaining: {current_hp}"); // Prints 75
}
```

### 2. Expressions vs Statements

Rust **is all about returning values**.

- **Statement:** Just an action. Always ends with `;` and returns nothing.

- **Expression:** Calculates and returns the result. **Ends WITHOUT `;`**.




```Rust
fn plus_one(x: i32) -> i32 {
x + 1 // No ';' - means this expression, the function will return x + 1
}

// ❌ TYPICAL ERROR:
fn plus_one_broken(x: i32) -> i32 {
x + 1; // Put ';' - turned into instructions! The function now returns nothing (unit type `()`).
}
```


## And another cool example for this mini topic

```Rust
fn five() -> i32 { // Indicate that the function MUST return a number of type i32
5 // Return 5 (Important: ';' DO NOT set this to be an expression!)
}

fn main() {
let x = five(); // Call the function and write the RETURNED result (5) to the variable x

println!("{x}"); // Prints 5!
}
```

### 3. How to pass variables to functions (The most important thing!)

There are 3 ways to "give" a variable to another function in Rust:

#### 🟢 Method 1: Give it away for good (Transfer of Ownership / Move)

If a type stores data on the heap (for example, `String`), then when passed to a function, the old variable **dies** and is no longer available in `main()`.



```Rust
fn print_message(text: String) { // accept it
println!("Message: {text}");
} // Here `text` is removed from memory!

fn main() {
let my_str = String::from("Hello");
print_message(my_str); // Give the variable

// ❌ ERROR! my_str no longer exists, it was given away by the function above!
// println!("{my_str}");
}
```

#### 🟡 Method 2: Give it a try / Read (Unmutable link `&`)

If a function just needs to **look** at the data without changing it, pass the reference using `&`. Ownership remains with `main()`.



```Rust
fn print_length(text: &String // Get it) {
println!("Length of line: {}", text.len());
} // The `text` link goes out of scope, but the line itself is not deleted!

fn main() {
let my_str = String::from("Hi, Rast!");
print_length(&my_str); // Pass the link using `&`

// ✅ EVERYTHING IS OK! The variable remained alive in main()
println!("The line is still here: {my_str}");
}
```

#### 🔴 Method 3: Give “drive and change” (Mutable link `&mut`)

If the function needs to **change** a variable, pass a mutable reference `&mut`. The variable in `main()` should also initially be `mut`.

``` Rust
fn add_exclamation(text: &mut String) {
text.push_str("!!!"); // Change the original by reference
}

fn main() {
let mut my_str = String::from("Hello");

add_exclamation(&mut my_str); // Pass a mutable reference `&mut`

// ✅ The result has changed right inside my_str!
println!("{my_str}"); // Prints: "Hello!!!"
}
```

### 💡 A short cheat sheet:

1. Should I just give the argument `i32`, `bool`, `f64`? → Pass it on as is (numbers are simply copied).

2. The function should only **read** the `String`/array/structure? → Use `&T` (link).

3. Should the function **change** the `String`/array/structure? → Use `&mut T` and make the variable `mut`.

4. Do you want to return the result without `return`? → Remove `;` in the last line of the function!
