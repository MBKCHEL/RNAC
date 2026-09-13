In Rust, strings of type `String` can be dynamically expanded (text added to them), but only if the variable is created as mutable (`mut`).

### 🛠️ Method `.push_str()`

This method takes your string and firmly attaches another text slice `&str` to the end of it.



```Rust
fn main() {
// 1. Be sure to write mut, otherwise the line cannot be changed
let mut item = String::from("Axe");

// 2. Add text to the end of the line
item.push_str("Blood");

println!("{item}"); // Outputs: "Bloody Axe"
}
```

### 🪓 What is the difference between `push_str` and `push`?

In Rust there are two similar methods for strings, do not confuse them in the code:

- **`.push_str("Text")`** - adds a whole **string slice** (text in double quotes).

- **`.push('🪓')`** - adds only **one single character** (delimited strictly by SINGLE quotes `' '`).



### How to use the push method:

```Rust
let mut craft = String::from("Craft");
craft.push_str(" weapons"); // Added a line
craft.push('!');           // Added one character
yoyo
