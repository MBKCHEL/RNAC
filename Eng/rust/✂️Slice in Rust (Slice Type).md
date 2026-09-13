> **The main point:** A slice (**Slice**) is a **link** to a sequential fragment of a collection (string, array, vector) **without copying** the data itself.

### 1. String slices (`&str`)

A string slice points to a portion of a `String` or a string literal.



```Rust
let s = String::from("hello world");

// Take fragments by indexes [beginning..end] (the end is not included!)
let hello: &str = &s[0..5];  // "hello"
let world: &str = &s[6..11]; // "world"
```

#### Range syntax abbreviations:

- `&s[0..5]` $\rightarrow$ `&s[..5]` _(if from the very beginning)_

- `&s[6..s.len()]` $\rightarrow$ `&s[6..]` _(if until the very end)_

- `&s[0..s.len()]` $\rightarrow$ `&s[..]` _(the whole object as a slice)_


> ⚠️ **Important:** String literals (`let s = "hello";`) **already** have type `&str`! This is a slice pointing to the program binary.

### 2. Slicing arrays and vectors (`&[T]`)

Slices work not only with strings, but also with any collections (arrays, vectors).



```Rust
let a = [1, 2, 3, 4, 5]; // Regular array like [i32; 5]

let slice: &[i32] = &a[1..3]; // Slice contains [2, 3]
```

- **Slice type:** `&[i32]` (read as _“reference to a slice of elements of type i32”_).

- It stores a pointer to the first element of the slice and its length.


### 3. Slices as Function Parameters (Best Practice!)

If you are writing a function that accepts a string, it is better to pass **`&str`** rather than `&String`. This makes the function universal!



```Rust
fn first_word(s: &str) -> &str {
    let bytes = s.as_bytes(); // Convert the string s into a slice of bytes

    for (i, &item) in bytes.iter().enumerate() {
        // i - index, item - byte (letter)
        if item == b' ' {
            // If we hit a space
            return &s[0..i]; // Return the slice from start to space
        }
    }

    &s[..] // If there is no space, return the entire string as a slice
}

fn main() {
    let s = String::from("hello world");

    let my_literal = "hello world";
    let _word2 = first_word(my_literal); // Works directly with &str literal

    let word = first_word(&s); // Pass a reference to String
    println!("{}", word); // Prints "hello"

    // s.clear(); // If you uncomment and move ABOVE println!, Rust will not compile the code!
}

```

### 💡 The main rules for memorizing slices:

1. **Do not own the data:** A slice is always a reference, it does not remove memory when the bracket closes.

2. **Safety:** If you have an immutable slice (`&str`), Rust **will not change** the original collection (`String`/array) while that slice is in use. This protects against errors!
