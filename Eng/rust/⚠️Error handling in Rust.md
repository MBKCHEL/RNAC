Rust doesn't have the usual `try/catch` exceptions. All errors are divided into two categories: **unrecoverable** (panic) and **recoverable** (`Result`).

### **1. Unrecoverable errors: `panic!` macro

The `panic!` macro forces the program to stop executing, print an error message, clear the stack, and terminate the process. Used when the program has entered an invalid state from which it cannot continue working.

**Main call options:**

- **Direct macro call:**



```Rust
panic!("Everything burned!"); // Terminates the program with this message
```

- **Automatic panic (for example, going beyond array boundaries):**



```Rust
years in = all!yu1, 2, 3sch;
v[99]; // Causes panic! (buffer overflow / out of bounds protection)
```

- **Unwrapping `Option` / `Result` via `.unwrap()` and `.expect()`:**



```Rust
let f = File::open("hello.txt").unwrap();
// If there is no file -> panic!

let f = File::open("hello.txt").expect("Failed to open file");
// If there is no file -> panic! with your custom text (Best Practice)
```


### **2. Setting behavior for `panic!`**

By default, when panicked, Rust does **unwinding** (stack unwinding): it goes backward through functions and clears memory. This can be changed in `Cargo.toml`:

,

```TOML
[profile.release]
panic = 'abort' # Immediately abort the process (reduces binary size)
```

 **For systems engineer / OS Dev:** The `RUST_BACKTRACE=1` flag in the terminal displays the full call stack (backtrace) in case of panic:
 
 ```bash
 RUST_BACKTRACE=1 cargo run
 ```

### **3. Recoverable errors: `Result<T, E>` enumeration**

If the error is expected (for example, the file was not found or the network has failed), `Result` is used:



```Rust
enum Result<T, E> {
Ok(T), // The operation was successful, returns the value T
Err(E), // An error occurred, returns error E
}
```

### **4. Operator `?` (Error Propagation)**

The `?` operator is a shorthand for passing an error further up the call stack. If a method returns `Err`, the `?` operator aborts the function and immediately returns this `Err`.



```Rust
use std::fs::File;
use std::io::{Self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
let mut username = String::new();

// If File::open returns Err, '?' will immediately return this Err from the function
File::open("hello.txt")?.read_to_string(&mut username)?;

Ok(username)
}
```

### **Short cheat sheet: When to use what?**

- **`panic!` / `.expect()`:** When writing examples, tests, prototypes or when the state of the program is **absolutely invalid** and it is dangerous to continue working.

- **`Result` / `?`:** In libraries, critical code and functions where the caller must decide how to handle the failure.
