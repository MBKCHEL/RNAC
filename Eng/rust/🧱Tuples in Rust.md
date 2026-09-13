## 2. Tuples (`tuple`) - Clear examples and real features

A tuple is needed when you need to temporarily connect several dissimilar values, but creating a separate structure (class/strect) for this purpose is too lazy or makes no sense.

### Example 1: Mini user database

```rust
fn main() {
    let vanya: (&str, u8, f64) = ("Vanya", 20, 1.9); // 1 tuple

    let danya: (&str, u8, f64) = ("Danya", 45, 2.0); // 2 tuple

    let (name_vanya, age_vanya, height_vanya) = vanya; // unpacking into variables

    let (name_danya, age_danya, height_danya) = danya; // unpacking into variables

    println!("Such indicators of 1 user: Years: {age_vanya}, Name: {name_vanya}, height: {height_vanya} meters"); // Conclusion
    println!("Such indicators for 2 users: Years: {age_danya}, Name: {name_danya}, height: {height_danya} meters"); // Conclusion
}

```

![[Pasted image 20260723124517.png]]
## It was necessary to put 2 different names in these variables, otherwise they would obscure each other.

### Example 2: Returning multiple values from a function

Unlike C#, where to return multiple variables from a method you need to fence in `out` parameters or tuples that load memory, in Rust this is done natively and as efficiently as possible.


```rust
// The function takes a string and returns a tuple: (status code, message text)
fn parse_data() -> (u16, &'static str) {
// ...some kind of logic...
(200, "Successfully processed") // return a tuple
}

fn main() {
let result = parse_data();

// Access by index via dot
println!("Code: {}, Answer: {}", result.0, result.1);
}
```

### Example 3: Destructuring (Unpacking) a Tuple

You don't have to write `.0`, `.1`. You can “parse” a tuple into individual variables on the fly. This is one of the main syntactic thrills of Rust.

Rust

```rust
fn main() {
// Tuple storing the server's IP address and port
let server_address: (&str, u16) = ("127.0.0.1", 8080);

// Unpack the tuple into two independent variables
let (ip, port) = server_address;

println!("The server is running on port {} at address {}", port, ip);
}
```
