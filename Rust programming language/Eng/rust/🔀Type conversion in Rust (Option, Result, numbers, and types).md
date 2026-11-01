Conversion in Rasta is the transformation of one data type into another. Allows you to avoid cumbersome `match` and handle errors neatly.

### 1. Turning `Result` into `Option` (`.ok()`)

Used when the result of an operation with an error (`Result<T, E>`) needs to be turned into a regular `Option<T>` (the error turns into `None`).



```Rust
let input = "100"; // Correct input
let parsed: Option<i32> = input.parse().ok(); // return Some(100)

let bad_input = "abc"; // Parsing error
let parsed_bad: Option<i32> = bad_input.parse().ok(); // return None
```

- **Why:** Ideal for parsing strings/input from a player, when we only care about the presence of a valid number, and the error itself is not interesting.


### 2. Turning `Option` into `Result` (`.ok_or()`)

Vice versa: turns `Option<T>` into `Result<T, E>` if you need to throw a clear error instead of an empty `None`.



```Rust
let player_name: Option<&str> = None;

// If None - turns into Err with our message
let result = player_name.ok_or("Player name not set!");
// result will become: Err("Player name not set!")
```

### 3. Transformation inside `Option` (`.map()`)

Allows you to apply a function to a value inside `Some` without touching `None`.



```Rust
fn plus_one(x: Option<i32>) -> Option<i32> {
x.map(|i| i + 1)
}

let hp = Some(99);
let new_hp = plus_one(hp); // return Some(100)

let no_hp = None;
let result = plus_one(no_hp); // return None
```

### 4. Converting references to values (`.copied()` and `.cloned()`)

When searching collections or dictionaries (`HashMap`), Rust returns references (`Option<&T>`). To get the value itself, use the `.copied()` or `.cloned()` methods.

- **`.copied()`** - for simple types (`i32`, `bool`, `f32`) that implement the `Copy` trait.

- **`.cloned()`** - for complex types (`String`, `Vec`) that require explicit cloning.




```Rust
let scores = vec![10, 20, 30];

// .find() returns Option<&i32>
// .copied() turns Option<&i32> into Option<i32>
let found: Option<i32> = scores.iter().find(|&&x| x == 20).copied();
```
### 5. Casting base types (`as`)

The `as` keyword is used to convert numeric types.



```Rust
let damage: f32 = 45.7;
let final_damage = damage as i32; // 45 (fractional part is discarded)

let level: u8 = 50;
let level_i32 = level as i32; // Convert to a wider type
```

> **Important:** When converting from larger types to smaller types using `as` (e.g. `i32 as u8`), a value overflow may occur!
