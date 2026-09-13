### 1. What are macros and why are they needed?

Macros are metaprogramming (code that writes code).

- **Main difference from functions:** Functions work with *values* at runtime. Macros work with the *abstract syntax tree (AST)* at compile time.

- **Example with `println!`:** A regular function in Rust cannot take a variable number of arguments of different types. The `println!` macro can:

```rust
println!("Hello"); // 1 argument
println!("Number: {}, text: {}", 10, "abc"); // 3 arguments of different types!
```

### 2. Why is the `!` (exclamation mark) needed?

- It signals to the compiler and the developer: "This is not a standard function call! Code generation happens here."

- This allows the compiler to check arguments before running the program. For example, if you pass 2 variables to `println!("{}", a, b)` but only specify one `{}` placeholder, **Rust will throw a compile-time error** instead of crashing at runtime!

### 3. Two main categories of macros in Rust

**A. Declarative Macros (`macro_rules!`)**

- The most common type (this is how `vec!`, `println!`, and `format!` are made).

- They work on the principle of **Pattern Matching**: inserting code depending on what you passed.

- Example of a simple macro:

```rust
macro_rules! say_hello {
    () => {
        println!("Hello!");
    };
}

fn main() {
    say_hello!(); // Expands into println!("Hello!");
}
```

**B. Procedural Macros** Accept Rust code as a token stream, modify it, and return new code. Divided into 3 types:

1. **Derive macros (`#[derive(...)]`):** Automatically generate trait implementations for structs.

```rust
#[derive(Debug, Clone)] // Calls macros to generate debugging and cloning code
struct User { name: String }
```

2. **Attribute-like macros (`#[tokio::main]`, `#[get("/")]`):** Attached to functions or structs and completely rewrite their internals.

3. **Function-like macros:** Called like `custom_macro!(...)`, but process contents with complex logic (e.g., `sqlx::query!`, which checks SQL queries directly against the database at compile time).

### 4. In short

In short, a macro is a thing that invokes code and generates it. For instance, when you need to pass a variable into a function, you create a template macro, then invoke it, pass the parameters, and that's it — basically, it's a boilerplate template.

### 5. Pros and cons of macros

- **Pros:**
  - Saves you from writing chunks of boilerplate code.
  - Safety: checks happen at compile time.
  - Allows creating convenient APIs (like the utility `vec![1, 2, 3]`).

- **Cons:**
  - Increases project compilation time.
  - Expanded code is hard to debug and read if the macro is overloaded with logic.