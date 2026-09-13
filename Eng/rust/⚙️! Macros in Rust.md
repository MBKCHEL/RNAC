### 1. What are macros and why are they needed?

Macros are metaprogramming (code that writes code).

- **Main difference from functions:** Functions work with _values_ during program execution (runtime). Macros operate on the syntax tree (AST) at compile time.

- **Example with `println!`:** A regular function in Rust cannot accept a variable number of arguments of different types. The `println!` macro can:



```Rust
println!("Hello"); // 1 argument
println!("Number: {}, text: {}", 10, "abc"); // 3 arguments of different types!
```



### 2. Why do we need `!` (exclamation mark)?

- Signals the compiler and programmer: “This is not a standard function call! This is where the code generation happens.”

- This allows the compiler to check arguments before running the program. For example, if in `println!("{}", a, b)` you pass 2 variables, and you specify one parenthesis `{}`, **Rust will throw an error during build**, and not crash in runtime!


### 3. Two main categories of macros in Rust

**A. Declarative macros (`macro_rules!`)**

- The most common type (this is how `vec!`, `println!`, `format!` are made).

- They work on the **Pattern Matching** principle: they substitute the code depending on what you passed.

- An example of a simple macro:



```Rust
macro_rules! say_hello {
    () => {
        println!("Hello!");
    };
}

fn main() {
    say_hello!(); // Expand to println!("Hello!");
}

```


**B. Procedural Macros** Take Rust code as a stream of tokens, modify it, and return new code. Divided into 3 types:

1. **Derive macros (`#[derive(...)]`):** Automatically generate trait implementations for structures.



```Rust
#[derive(Debug, Clone)] // Calls macros to generate debug and clone code
struct User { name: String }
```

2. **Attribute macros (`#[tokio::main]`, `#[get("/")]`):** They are attached to functions or structures and completely rewrite their internals.

3. **Function-like macros:** Called as `custom_macro!(...)`, but process the contents with complex logic (for example, `sqlx::query!`, which checks SQL queries directly in the database at compile time).

### 4. In short

In short, a macro is a thing that calls code and does it, like you need to throw a variable into a function, you make a template macro, then you call it, throw parameters and that’s it, in short, it’s a template

### 5. Pros and cons of macros

- **Pros:**

- Eliminates the need to write packs of template code (boilerplate).

- Security: checks occur at the compilation stage.

- Allows you to create convenient APIs (like the `vec![1, 2, 3]` utility).

- **Cons:**

- Increases project compilation time.

- Deployable code is difficult to debug and read if the macro is overloaded with logic.
