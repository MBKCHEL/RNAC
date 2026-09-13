### 1. Main idea

There is **no concept of `null` / `nullptr`** in Rust.

Instead, the built-in enum is used - **`Option<T>`**. It explicitly forces you to handle cases where there is a value and when there is not.



```Rust
enum Option<T> {
Some(T), // Value IS (wraps data of type T)
None, // NO values
}
```

### 2. Why is this necessary?

- **Safety:** The compiler will not allow you to use data from `Option` until you explicitly get it from there. It is physically impossible to forget to check for `null`.

- **Predictability:** The function signature immediately tells whether it can return "void".


### 3. Basic example



```Rust
fn find_number(flag: bool) -> Option<i32> {
    if flag {
        Some(42) // Success: return a number
    } else {
        None // Nothing found
    }
}

```

### 4. Ways to work with `Option` (how to pull out a value)

#### A. Via `match` (full control)

Takes control of both scenarios.



```Rust
fn main() {
    match find_number(true) {
        Some(val) => println!("Found the number: {val}"),
        None => println!("Nothing found!"),
    }
}

// HERE IT IS, THE FUNCTION!
fn find_number(flag: bool) -> Option<i32> {
    if flag {
        Some(42)
    } else {
        None
    }
}

```

#### B. Via `if let` (quickly get `Some`)

Used if `None` processing is not needed.



```Rust
if let Some(val) = find_number(true) {
println!("Number: {val}");
}
```

#### C. Method `.unwrap_or()` (with default value) - _Safe_

If inside is `Some`, will return the value. If `None` - will return the passed default.



```Rust
let number = find_number(false).unwrap_or(0); // return 0
```

#### D. Methods `.unwrap()` and `.expect()` - _Danger!_

They get the value directly, **BUT if there is `None`, the program will fall into `panic!`**.



```Rust
let val1 = find_number(true).unwrap(); // Return 42
let val2 = find_number(false).expect("Expected a number!"); // Will crash with your mistake
```

### 5. Brief cheat sheet

| **Concept / Method**            | **What it does**                                         |
| ------------------------------- | -------------------------------------------------------- |
| **`Some(v)`**                   | Wrap over existing value `v`.                            |
| **`None`**                      | Denotes the absence of a value.                          |
| **`if let Some(x) = opt`**      | Executes a block of code only if there is `Some` inside. |
| **`.unwrap_or(default)`**       | Safely extracts the value or substitutes `default`.      |
| **`.is_some()` / `.is_none()`** | Returns `true`/`false` to check the status.              |
