### 1. The main law of Rust: Immutability by default

Unlike C# and most languages, in Rust any variable out of the box is a **constant**.


```rust
let x = 5;
x = 6; // ❌ ERROR! The compiler will give you a hand.
```

In order for a variable to be changed, you need to explicitly add **`mut`** (mutable):


```rust
let mut x = 5;
x = 6; // ✅ Successfully changed!
```

### 2. Integers

In Rust, number types are named by their bit size. Very logical and systematic.

| **Signed type (can be -)** | **Unsigned type (+ only)** | **Size in memory** |
| -------------------------------- | ---------------------------- | ------------------------------------------ |
| `i8` | `u8` | 8 bits (1 byte) |
| `i16` | `u16` | 16 bits (2 bytes) |
| `i32` _(default)_ | `u32` | 32 bits (4 bytes) |
| `i64` | `u64` | 64 bits (8 bytes) |
| `i128` | `u128` | 128 bits (16 bytes) |
| `isize` | `usize` | Depends on PC architecture (32 or 64 bit) |

> 📌 **Important for gamedev:** `usize` is almost always used for **array indexes** and loops, because it is equal to the size of the pointer in your PC's memory.

### 3. Fractional numbers (Floats)

Everything is simple here, there are only two types (in C# these are `float` and `double`):

- `f32` - single precision (what Unity uses everywhere for coordinates and physics).

- `f64` _(default)_ - double precision (more accurate calculations).



```rust
let distance: f32 = 10.5;
```

### 4. Boolean Type and Symbols

- **Boolean (`bool`):** Traditional `true` or `false`. Occupies 1 byte.

- **Characters (`char`):** A single character within **single quotes**. In Rust, `char` weighs as much as 4 bytes, because it supports all Unicode characters (even emoji!).



```rust
let is_running: bool = true;
let icon: char = '🦀'; // Yes, this is a valid char!
```

### 5. Text Hell: Strings (Introduction)

Strings in Rust are a particular pain point for beginners, because there are... several types. But for the base you need to know two main things:

1. **`&str` (String slice/literal):**

This is text hardwired into the program itself. It is immutable. When you write text in quotes, it's `&str`.


```rust
let status = "Alive"; // Type: &str
```

2. **`String` (Dynamic string):**

This is a full-fledged string that is created in the heap. It can be expanded, changed, and symbols can be added to it.


```rust
let mut player_name = String::from("Vanya"); // Created String
player_name.push_str(" _Dev"); // Changed the line
```


### 6. Rust feature: Shadowing

In Rust, you can declare a new variable with **exactly the same name** as the old one. This is called shading. The old variable seems to “die”, and its place is taken by a new one, and it may even be of a _different type_!


```rust
let spaces = " "; // This is a string (&str)
let spaces = spaces.len(); // Now spaces is a number (usize) equal to 3!
```

_Why?_ So as not to come up with stupid names like `spaces_str`, `spaces_int`, but to beautifully reuse one variable within one block of code.
