- # 1. len(); - displays the number of BYTES. Exactly bytes, not characters, example in code:
```rust
fn main() {
let x = "ggg";  // Set the value of the variable

let x = x.len(); // Shadow the x variable and print the number of BYTES

println!("{x}");  // Prints 3
}
```
## The main joke len(); it outputs BYTES if, for example, I write a variable: let X = "Дилдо"; It displays not 5, but 10, because the Cyrillic alphabet takes up 2 bytes of RAM.


- # `as_bytes()` in Rust

> **The main gist:** The `as_bytes()` method takes a string (`String` or `&str`) and returns a **slice of bytes** (`&[u8]`). It does not copy the text, but simply allows you to look at what this text consists of in memory.

### 1. Why convert a string to bytes at all?

For humans, a string is a set of letters (`'h'`, `'e'`, `'l'`).

To a computer, a string is simply a sequence of numbers (bytes) encoded **UTF-8**.

- The letter `'h'` in memory is the number `104`.

- The letter `'e'` is `101`.

- The space `' '` is the number `32`.


Checking bytes in a loop for Rust is **hundreds of times faster** than parsing complex character-by-character structures.

### 2. Simple example:



```Rust
fn main() {
let text = "hello";

// Convert the string into a byte slice
let bytes = text.as_bytes();

println!("{:?}", bytes);
// Outputs: [104, 101, 108, 108, 111]
}
```

### 3. What the heck is `b' '` (Byte Literal)?

In the code above you saw: `if item == b' '`.

The letter `b` before the quotes turns the character into its numeric byte code:

- `' '` is the space character.

- `b' '` is a **byte** (the number `32`) indicating a space.


We converted the string to bytes (`as_bytes()`), so we must compare elements **with bytes** (`b' '`), and not with ordinary letters!

### 💡 Summary in three words:

1. `s.as_bytes()` $\rightarrow$ turns text into a list of byte numbers `[104, 101, 108...]`.

2. `b' '` $\rightarrow$ byte code of the space (number `32`).

3. The computer quickly compares **number with number**, making the space search work instantly!

- # 2. \t and \n

\t represents a tab, and \n represents a newline, meaning:

This is \n

```rust

fn main() {
    println!("Hello\n, world!");
}
```

Output:
```bash
Hello
, world!
```

This is \t
```rust
fn main() {
    println!("Hello");
    println!("\tworld!");
}
```

Output:
```bash
Hello
        world!
```