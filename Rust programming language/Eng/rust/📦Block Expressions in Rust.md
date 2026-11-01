### 1. Main idea

In Rust, the curly braces `{ ... }` are not just a code separator, but an **expression that returns a value**.



```Rust
let result = {
let a = 10;
let b = 20;
a + b // 👈 NO semicolon! The block will return 30
};

println!("{result}"); // Prints 30
```

## Another example:
```Rust
fn main() {
let x = 5;// Make variable X

let y ={
let x = 3;  // In this block, the variable X is temporarily obscured
x+1
}; // At the end, variable X dies, but it is reborn in variable y, and our 1 variable X appears back.

println!("The value of y is: {}", y);
}
```


### 2. The golden rule of the semicolon `;`

- **Without `;` at the end of the block:** The block “spits” the value out into the variable.

- **With `;` at the end of the block:** The value is "eaten" and the block returns nothing (empty type `()`).




```Rust
// ❌ Logic error (forgot to remove ';'):
let result = {
let a = 10;
a + 5; // Put ';'! Now result is () instead of 15
};
```

### 3. Insulation and protection from debris (Scope)

All variables created inside `{ ... }` **die immediately when leaving the parentheses**.

This is necessary to calculate complex things without clogging up the RAM and variable names in the main function.



```Rust
fn main() {
let final_damage = {
let base = 50;
let crit_multiplier = 2;
base * crit multiplier
};

// ❌ Here `base` and `crit_multiplier` NO LONGER EXIST!
// println!("{base}"); // Compilation error!

// ✅ Only pure result is available:
println!("Damage: {final_damage}"); // 100
}
```

### 4. Shadowing inside blocks

If you re-declar a variable with the same name inside `{ ... }`, it will “override” the outer one, but **only while the code is inside these brackets**.



```Rust
let x = 5; // External x

let y = {
let x = 3; // Inner x (shaded outer)
x + 1 // Returns 4
};

println!("y = {y}"); // Outputs: y = 4
println!("x = {x}"); // Outputs: x = 5 (outer x remains safe and sound!)
```

### 💡 Memory cheat sheet:

1. Need to assemble a complex object/number from a bunch of small calculations? → **Pack in `let x = { ... };`**.

2. Do you want to return a value from a block? → **Don't put `;` on the last line!**

3. Everything that was declared inside `{}` - **disappears forever** after the closing `}`.
