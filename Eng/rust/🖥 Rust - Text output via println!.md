`println!` (print line) is the main tool for outputting text to the console (terminal) with automatic line breaking at the end.

### ⚠️ The exclamation point `!` is important!

The `!` symbol at the end means that this is **not a regular function (method), but a MACRO**.

- **Why a macro?** A regular function in Rust requires a strictly fixed number of arguments of the same type. The `println!` macro is a mini code generator. It can accept as many variables of different types as you like and “under the hood” itself collects them into one line.

- **Safety:** If you specified a location for a variable, but forgot to pass it, the compiler will throw an error _before_ the program starts.


### 🚀 Use options (Cheat Sheet)

#### 1. Plain text output


```rust
println!("Hello, gamedev!");
```

#### 2. Variable output (using `{}`)

The curly braces `{}` are a “placeholder” (empty space) where the compiler will substitute the value of the variable separated by commas.


```rust
let player_x = 5;
println!("Player X coordinate: {}", player_x);
// Outputs: Player's X coordinate: 5
```

#### 3. Printing multiple variables at once

Variables are substituted strictly in order: the first variable - in the first brackets, the second - in the second.


```rust
let x = 5;
let y = 10;
println!("Player is at position X: {}, Y: {}", x, y);
// Output: Player is at position X: 5, Y: 10
```

#### 4. Direct output of calculations

Inside the brackets, in place of the variables, you can immediately write mathematics:


```rust
println!("Result: {}", 2 + 2);
// Outputs: Result: 4
```
