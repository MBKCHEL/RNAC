#### What is a tuple?

A tuple is a compound data type. It combines several values into a single whole. Its features:

- values can be of **different types**, for example `(&str, u8, f64)`;
- the size is **fixed** and known at compile time: after creation, you can’t add or remove elements;
- elements are stored **in order** and have no names, so you access them by index: `.0`, `.1`, and so on;
- it is written in parentheses, separated by commas: `(1, "text", 3.5)`.

#### Why use it?

A tuple is useful when you need to group several values, especially of **different types**, and creating a separate structure (`struct`) for it makes no sense. 

##### Example 1: Mini user database

```Rust
fn main() {
    let vanya: (&str, u8, f64) = ("Vanya", 20, 1.9); // tuple 1: name, age, height
    let danya: (&str, u8, f64) = ("Danya", 45, 2.0); // tuple 2

    let (name_vanya, age_vanya, height_vanya) = vanya; // unpacking into variables
    let (name_danya, age_danya, height_danya) = danya; // unpacking into variables

    println!("User 1 stats: Age: {age_vanya}, Name: {name_vanya}, Height: {height_vanya} m");
    println!("User 2 stats: Age: {age_danya}, Name: {name_danya}, Height: {height_danya} m");
}
```

I had to give the variables for the two tuples different names. If they had the same names, the second `let` would shadow the first one, and the values of the first tuple would become inaccessible.

##### Example 2: Returning multiple values from a function

In Rust, a function can return several values at once through a tuple. You need neither a separate structure nor `out` parameters.

```Rust
// The function returns a tuple: (status code, message text)
fn parse_data() -> (u16, &'static str) {
    // ...some logic...
    (200, "Processed successfully") // return the tuple
}

fn main() {
    let result = parse_data();

    // Access elements by index using a dot
    println!("Code: {}, Response: {}", result.0, result.1);
}
```

##### Example 3: Destructuring (unpacking) a tuple

You don’t have to access elements through `.0` and `.1`. You can break a tuple apart into separate variables right away, which is usually more readable than using indexes.

```Rust
fn main() {
    // A tuple holding a server's IP address and port
    let server_address: (&str, u16) = ("127.0.0.1", 8080);

    // Unpack into two independent variables
    let (ip, port) = server_address;

    println!("Server is running on port {} at address {}", port, ip);
}
```