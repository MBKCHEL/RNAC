### 1. What is it?

`Ordering` is an **enum** from the standard library (`std::cmp::Ordering`). It is used to represent the **result of comparing** two values.

### 2. Three possible options (`enum variants`)

`Ordering` has exactly three states:

| **Option** | **What does it mean** | **Example (a relative to b)** |
| ----------------------- | ---------------- | ----------------------------- |
| **`Ordering::Less`** | Less | `5` is less than `10` |
| **`Ordering::Greater`** | More | `10` over `5` |
| **`Ordering::Equal`** | Equals | `5` is equal to `5` |
|                         |                  |                               |

### 3. How does this work in code?

For comparison, the `.cmp()` method is used, which returns one of three `Ordering` options:



```Rust
use std::cmp::Ordering; // Connect enum

let secret_number = 42;
let guess = 10;

// The .cmp() method compares guess with secret_number
match guess.cmp(&secret_number) {
Ordering::Less => println!("Too little!"),
Ordering::Greater => println!("Too much!"),
Ordering::Equal => println!("You guessed it right!"),
}
```

### 💡 Main tips to remember:

- **Combination with `match`:** `Ordering` works perfectly with the `match` construct. **all 3 options** are tried - if you skip even one, the Rust compiler will throw an error (it monitors completeness).

- **Passing by reference:** The `.cmp(&secret_number)` method accepts a **reference** (`&`) to avoid taking ownership of the variable.
