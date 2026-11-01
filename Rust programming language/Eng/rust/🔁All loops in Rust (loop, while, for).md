Rust has **three main types of loops**. Everyone has their own task.

### 1. Endless loop - `loop`

It runs indefinitely until you forcefully stop it using `break`.

- **Why is it needed:** When you do not know in advance how many times the code should be executed (for example, a game loop, waiting for user input in a guessing game, or processing network requests).

- **Trick:** `loop` can **return a value** via `break`!




```Rust
fn main() {
    let mut counter = 0;

    // loop can return the result to a variable!
    let result = loop {
        counter += 1;

        if counter == 10 {
            break counter * 2; // Exit the loop and return 20
        }
    };

    println!("Result: {result}"); // Prints 20
}

```

### 2. Conditional loop - `while`

Executes a block of code until **condition is `true`**.

- **Why is it needed:** When the loop must spin while some condition is met.

- **Minus:** If you use `while` to iterate through an array by index, it is slow (Rust checks at each iteration to see if we have gone beyond the bounds of the array) and unsafe.




```Rust
fn main() {
    let mut number = 3;

    while number != 0 {
        println!("{number}!");
        number -= 1;
    }

    println!("LET'S GO!");
}
```

### 3. The king of iterations is `for`

The safest, fastest and idiomatic (correct) loop in Rust. Used to iterate over collections, ranges and iterators.

- **Why is it needed:** Go through the elements of an array, a range of numbers, etc.

- **Plus:** It **doesn't do unnecessary bounds checks** (the compiler optimizes it to the skies) and completely protects against array out-of-bounds.


#### Example A: Iterating over a range of numbers



```Rust
fn main() {
    // 1..4 means from 1 to 3 (4 is not included)
    for number in 1..4 {
        println!("{number}!");
    }

    // 1..=3 means from 1 to 3 inclusive
    // .rev() expands the range backwards!
    for number in (1..=3).rev() {
        println!("{number}!");
    }
}

```

#### Example B: Looping through array elements



```Rust
fn main() {
    let a =;

    // Go through each element directly!
    for element in a {
        println!("Value: {element}");
    }
}

```

### 🛑 Loop management: `break` and `continue`

1. **`break`** - immediately breaks out of the loop.

2. **`continue`** - skips the rest of the current lap and immediately moves on to the next one.


#### 🏷 Loop Labels

If you have a loop inside a loop, and you need to jump out of the deepest `loop` from the **outer one** - use labels with a single quote ``name`:



```Rust
'outer: loop {
    println!("Entered outer loop");

    loop {
        println!("Entered inner loop");

        // We break the EXTERNAL loop!
        break 'outer;
    }
}

```

### 🧠 Brief cheat sheet: What and when to use?

|**Cycle**|**When to use?**|**Example**|
|---|---|---|
|**`loop`**|Endless process or need to return value from `break`.|Game, menu, input to win (`match`).|
|**`while`**|Repeat until the simple condition `true/false` is true.|Count until the stop flag is reached.|
|**`for`**|You need to go through a range (`1..10`) or a collection (array).|Iterate through a list, items in the inventory, frames.|
