## Constants (`const`) - Clear examples of real-life applications

A constant in Rust is not just a variable that cannot be changed. This is the value that the compiler **physically embeds (copies)** into every place in the code where you use it. They are calculated _before_ the program is launched.

### Example 1: Setting System Limits (Buffer Size)

Imagine that you are writing a network utility or file parser. You need to hardcode the size of the data packet to be read in bytes.


```rust
// Declare globally (outside functions). The `usize` type is ideal for memory sizes.
const MAX_PACKET_SIZE: usize = 4096;

fn main() {
    // We can use a constant to set the size of the array!
    // A regular `let` variable cannot be substituted here; the compiler will not allow it.
    // in the line [0; MAX_PACKET_SIZE]; 0 is what we will fill the array with, and the tuple contains 4096, which means we will make an array with 4096 elements, and each element will have 0 since we did auto-fill with zero
    let buffer: [u8; MAX_PACKET_SIZE] = [0; MAX_PACKET_SIZE];

    println!("A buffer of {} bytes has been allocated.", MAX_PACKET_SIZE);
}

```

### Example 2: Formulas and mathematical coefficients

Constants can be calculated at compile time based on other constants!

```rust
const SECONDS_IN_MINUTE: u64 = 60;
const MINUTES_IN_HOUR: u64 = 60;

// The compiler itself will multiply this ONCE during assembly. In runtime, the processor will not waste time on multiplication.
const SECONDS_IN_HOUR: u64 = SECONDS_IN_MINUTE * MINUTES_IN_HOUR;

fn main() {
println!("Seconds in hour: {}", SECONDS_IN_HOUR);
}
```
