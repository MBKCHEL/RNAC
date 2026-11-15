### 📝 `format!` and `write!`: building text from pieces

>  **If you don’t know what macros are and why there’s a `!` at the end, check this file:** [[! Macros in Rust|Click here!]]

#### ❓ 1. What problem do these macros solve

Very often you need to build **one string out of several pieces**: some ready-made text plus the values of variables. For example, from the word “Hello”, a name and an age you want to get “Hello, Spartacus! You are 30 years old.”

Both macros take a **template** (text with “holes”) and **data** (what goes into the holes). The only difference is **where** the finished text ends up:

|Macro|Where the text goes|What it returns|
|---|---|---|
|`format!`|A **new** string is created|A ready `String`|
|`write!`|It is **appended** to an already existing place (a string, a file, a buffer)|A `Result`: whether the write succeeded or not|

---

#### 💻 2. `format!`: build a new string

```Rust
fn main() {
    let name = "Spartacus";
    let age = 30;

    let text = format!("Hello, {name}! You are {age} years old.");

    println!("{text}"); // Hello, Spartacus! You are 30 years old.
}
```

##### 🔍 Breaking it down

- **`{name}` and `{age}`**: these are the “holes”. Rust looks for variables with the same names and substitutes their values. The variables must exist **before** `format!` is called.
- **`let text = format!(...)`**: the macro **returns** a ready `String`. It doesn’t print anything to the screen by itself, it only builds the text.

##### 🔢 Three ways to fill the holes

**By variable name** (shown above): `{name}`.

**By order.** Leave the holes empty and list the values **after the template, separated by commas**:

```Rust
fn main() {
    let a = 5;
    let b = 7;

    let text = format!("{} + {} = {}", a, b, a + b);

    println!("{text}"); // 5 + 7 = 12
}
```

The first hole takes the first value, the second takes the second, the third takes the third. If there are three holes but only two values, you get an error **at compile time**, not while the program is running.

**By index or by your own name.** This is useful when one value must be inserted several times, or when you want readable names:

```Rust
fn main() {
    // By index: {0} is the first value, {1} is the second
    let a = format!("{0} versus {1}, and {1} versus {0}", "Gaius", "Marcus");

    // By your own name: name = value after the comma
    let b = format!("{hero} defeated {enemy}", hero = "Gaius", enemy = "Marcus");

    println!("{a}");
    println!("{b}");
}
```

>  **TRAP:** Inside the `{}` itself you can write **only a name**. The line `format!("{a + b}")` will not compile. An expression (`a + b`) is passed after the comma, or computed beforehand into a separate variable.

##### 📦 `format!` doesn’t take your variables

Remember ownership? `format!` only **looks** at your variables, as if through `&`. So they stay alive after the call:

```Rust
fn main() {
    let name = String::from("Spartacus");

    let text = format!("Hello, {name}!");

    println!("{name}"); // WORKS! name wasn't lost
    println!("{text}");
}
```

##### 🧰 What to do with the result

The result is a regular `String`, so you can use it like any other string.

**Return it from a function:**

```Rust
fn greeting(name: &str) -> String {
    format!("Hello, {name}!") // no semicolon: this is the value the function returns
}

fn main() {
    let text = greeting("Gaius");
    println!("{text}"); // Hello, Gaius!
}
```

**Append it to another string:**

```Rust
fn main() {
    let mut message = String::from("Result: ");

    let number = format!("{}", 42);
    message.push_str(&number);

    println!("{message}"); // Result: 42
}
```

**Pass it to a function:**

```Rust
fn shout(text: &str) {
    println!("{text}!!!");
}

fn main() {
    let text = format!("I am {} years old", 30);
    shout(&text); // we pass a reference, text stays with us
}
```

##### 🆚 Why `format!` if there’s `+`

```Rust
fn main() {
    let name = "Spartacus";
    let age = 30;

    // With format!: short and readable
    let text = format!("Hello, {name}! You are {age} years old.");

    // With +: longer, and the number has to be converted to a string by hand
    let text2 = String::from("Hello, ") + name + "! You are " + &age.to_string() + " years old.";

    println!("{text}");
    println!("{text2}");
}
```

The result is the same, but `format!` does everything for you: it turns numbers into text, and it reads like a finished sentence.

---

#### 🎛️ 3. The template: this is where `format!` shows its power

So far the holes contained only a name. But after the name you can put a **colon** and write **formatting rules**: how to show the value, what width, what alignment. All of this works the same way in `format!`, `write!` and `println!`.

The general form of a hole:

```
{ name : rules }
```

##### 👁️ 3.1. `{}` and `{:?}`: two ways to show a value

Every value can have two views: one **for people** and one **for the programmer**.

- **`{}`** (Display): a nice view for people. Not everything has it.
- **`{:?}`** (Debug): a debug view, “how it’s built inside”. Almost everything has it.
- **`{:#?}`**: the same as `{:?}`, but with line breaks and indentation (handy for big data).

```Rust
fn main() {
    let numbers = vec![1, 2, 3];
    let maybe = Some(5);
    let word = "hi";

    println!("{numbers:?}"); // [1, 2, 3]
    println!("{maybe:?}");   // Some(5)
    println!("{word}");      // hi
    println!("{word:?}");    // "hi"  (Debug shows the quotes)

    // println!("{numbers}"); // ERROR! Vec has no view "for people"
}
```

If the compiler says that a type “doesn’t implement `std::fmt::Display`”, you need to use `{:?}` (or write your own Display, covered in section 4).

For **your own struct**, Debug is obtained with a single line, `#[derive(Debug)]`, above it:

```Rust
#[derive(Debug)]
struct Warrior {
    name: String,
    hp: u32,
}

fn main() {
    let w = Warrior { name: String::from("Spartacus"), hp: 100 };

    println!("{w:?}");
    // Warrior { name: "Spartacus", hp: 100 }

    println!("{w:#?}");
    // Warrior {
    //     name: "Spartacus",
    //     hp: 100,
    // }
}
```

##### 📐 3.2. The full formula of the rules

The rules go **in exactly this order** (each one can be skipped):

```
{ : [fill][alignment][sign][#][0][width][.precision][type] }
```

|Part|What it does|Example|
|---|---|---|
|fill|The character used to fill empty space (a space by default)|`*`|
|alignment|`<` left, `^` center, `>` right|`>`|
|sign|`+` shows the sign of numbers|`+`|
|`#`|The “pretty” form (the `0x` prefix, or `{:#?}`)|`#`|
|`0`|Pad a number with zeros on the left|`0`|
|width|Minimum width in characters|`10`|
|`.precision`|Digits after the decimal point (for floats) or max characters (for strings)|`.2`|
|type|How to show a number: `x`, `b`, `e`, etc.|`x`|

Don’t be scared, we’ll go through it piece by piece.

##### ↔️ 3.3. Width and alignment

Width is the **minimum** number of characters. If the text is shorter, the rest is filled with spaces. If it’s longer, it is not cut off.

```Rust
fn main() {
    println!("[{:8}]", "Sword");   // [Sword   ]  strings are pushed to the left by default
    println!("[{:8}]", 42);        // [      42]  numbers are pushed to the right by default

    println!("[{:<8}]", "Sword");  // [Sword   ]  left
    println!("[{:>8}]", "Sword");  // [   Sword]  right
    println!("[{:^8}]", "Sword");  // [ Sword  ]  center
}
```

Want to fill with something other than spaces? Put the character **before** the alignment:

```Rust
fn main() {
    println!("{:*^20}", "Sword");   // *******Sword********
    println!("{:-<15}", "Shield");  // Shield---------
    println!("{:.>15}", "Helmet");  // .........Helmet
}
```

Width is counted in **characters**, not bytes, so non-English text lines up fine too.

##### 🔢 3.4. Precision, sign and zeros

```Rust
fn main() {
    let pi = 3.14159265;

    println!("{pi:.2}");    // 3.14      two digits after the decimal point
    println!("{pi:.0}");    // 3         zero digits
    println!("{pi:8.3}");   //    3.142  width 8, three digits after the decimal point
    println!("{pi:08.3}");  // 0003.142  the same, but with zeros on the left

    println!("{:05}", 42);  // 00042     pad with zeros to width 5
    println!("{:+}", 7);    // +7        show the sign
    println!("{:+}", -7);   // -7

    println!("{:.3}", "Hello"); // Hel  for strings, precision means max characters
}
```

##### 🧮 3.5. Number systems

For a low-level programmer this is very useful:

```Rust
fn main() {
    let n = 255;

    println!("{n}");      // 255         plain (decimal)
    println!("{n:x}");    // ff          hexadecimal, lowercase letters
    println!("{n:X}");    // FF          hexadecimal, uppercase letters
    println!("{n:#x}");   // 0xff        with the 0x prefix
    println!("{n:b}");    // 11111111    binary
    println!("{n:#b}");   // 0b11111111  binary with prefix
    println!("{n:o}");    // 377         octal

    println!("{:08b}", 5);   // 00000101    binary, zero-padded to 8 digits
    println!("{:#010b}", 5); // 0b00000101  with prefix (width 10 includes “0b”)
    println!("{:e}", 1500.0); // 1.5e3      scientific notation
}
```

The line `{:08b}` reads like this: “with zeros (`0`), up to width 8 (`8`), in binary (`b`)”.

##### 🪄 3.6. Width and precision from a variable

If you can’t write the width as a number in advance, use a variable name with a `$` at the end:

```Rust
fn main() {
    let width = 12;
    let prec = 3;

    println!("[{:>width$.prec$}]", 3.14159265);
    // [       3.142]
}
```

`width$` means “take the width from the variable `width`”, and `prec$` means “take the precision from the variable `prec`”.

##### 🚪 3.7. How to print the curly braces themselves

Since `{` and `}` are taken by the holes, to show the braces themselves you **double** them:

```Rust
fn main() {
    let name = "Gaius";
    let text = format!("{{name}} = {name}");

    println!("{text}"); // {name} = Gaius
}
```

##### 🧾 3.8. Putting it all together: an even table

```Rust
fn main() {
    let items = [("Sword", 150.5), ("Shield", 80.0), ("Helmet", 45.25)];

    println!("{:<10}|{:>8}", "Item", "Price");
    println!("{:-<10}+{:->8}", "", "");

    for (name, price) in items {
        println!("{name:<10}|{price:>8.2}");
    }
}
```

Output:

```
Item      |   Price
----------+--------
Sword     |  150.50
Shield    |   80.00
Helmet    |   45.25
```

The item name is left-aligned and takes 10 characters, the price is right-aligned, takes 8 and shows two digits after the decimal point.

---

#### 🖊️ 4. `write!`: append to an already existing place

`format!` creates a **new** string every time. But `write!` writes **wherever you point it**: into an already existing `String`, into a file, into the console. Its template is **exactly the same** as in `format!`, only one more argument is added before the template: **where to write**.

```
format!( template, data )
write!( where_to_write, template, data )
```

There is also the `writeln!` version: the same thing, but it adds a line break at the end of the text.

##### 📜 4.1. Writing into a `String`

```Rust
use std::fmt::Write; // IMPORTANT! Without this line write! won't work for String

fn main() {
    let mut text = String::new(); // an empty string, mut because we'll be appending

    write!(text, "Hello, {}!", "Gaius").unwrap();
    writeln!(text, " You are {} years old.", 30).unwrap();
    write!(text, "The end.").unwrap();

    println!("{text}");
}
```

Output:

```
Hello, Gaius! You are 30 years old.
The end.
```

##### 🔍 Breaking it down

- **`use std::fmt::Write;`**: this is a trait, like `RngExt` in the notes about `rand`. Without the import Rust will say that `String` has no method `write_fmt`. If you see that error, you simply forgot this line.
- **`let mut text`**: `mut` is required because we **change** the string by appending to it.
- **`write!(text, ...)`**: the first argument is **where to write**. You don’t need to write a reference (`&mut text`), the macro takes it itself.
- **`writeln!`**: added a line break after the text by itself. That’s why “The end.” ended up on a new line.
- **`.unwrap()`**: explained in the next point.

##### ❓ 4.2. What is `.unwrap()` and why does `write!` return a `Result`

`format!` simply returns a string, and nothing can go wrong. But `write!` writes into someone else’s place, and there a failure **can theoretically** happen (for example, the disk ran out of space for a file). That’s why it returns a **`Result`**: “did it work or not”.

If you ignore the result, the compiler gives a warning, `unused Result that must be used`. There are three ways to handle it:

- **`.unwrap()`**: “if there’s an error, crash the program”. For writing into a `String` this is safe, because writing into a string in memory doesn’t fail.
- **`?`**: “if there’s an error, immediately exit the function and return that error upward”. Works only in a function that itself returns a `Result`. This is how it’s usually written for files.
- **`let _ = write!(...);`**: “I don’t care whether it worked or not”.
- *More details in the file*: [[Error handling in Rust|Click here!]]

##### 🔁 4.3. Why write into a `String` if there’s `format!`

The main reason is **loops**. Say you need to build a long text out of many pieces.

```Rust
use std::fmt::Write;

fn main() {
    let mut report = String::new();

    for i in 1..=3 {
        writeln!(report, "Line number {i}").unwrap();
    }

    print!("{report}"); // print! is println! without the extra line break
}
```

Output:

```
Line number 1
Line number 2
Line number 3
```

If we built this text with `format!`, we’d have to create a new temporary string at every step and then glue it onto the main one. But `write!` appends **straight into the string you need**, without extra copies. That’s both shorter and faster.

##### 📁 4.4. Writing into a file and into the console

Here you need a **different** trait, from a different place:

```Rust
use std::fs::File;
use std::io::Write; // for files and the console it's std::io::Write (not fmt!)

fn main() -> std::io::Result<()> {
    let mut file = File::create("hello.txt")?;

    writeln!(file, "Hello, {}!", "file")?;
    writeln!(file, "I am {} years old.", 30)?;

    Ok(())
}
```

After running it, a file `hello.txt` with two lines appears.

##### 🔍 Breaking it down

- **`use std::io::Write;`**: a trait for writing to the outside world (files, console, network). It is **not** the same as `std::fmt::Write` for strings.
- **`fn main() -> std::io::Result<()>`**: we allow `main` to return a result. This is needed to be able to use `?`.
- **`File::create("hello.txt")?`**: creates the file. `?` means “if creating failed (for example, no permissions), exit `main` with an error”.
- **`writeln!(file, ...)?`**: writes a line into the file and checks the result the same way, via `?`.
- **`Ok(())`** at the end: “everything went fine, there’s nothing to return”. `()` is the empty value.

The same works for the console:

```Rust
use std::io::Write;

fn main() -> std::io::Result<()> {
    let mut out = std::io::stdout(); // "where to write" is the standard output

    writeln!(out, "Hello from stdout, I am {} years old", 30)?;

    Ok(())
}
```

>  **TRAP:** Both traits are called `Write`. If you need both in one file, you’ll get a name conflict. The fix: import one of them under a different name:

```Rust
use std::fmt::Write as FmtWrite;
use std::io::Write;
```

> Or, if you only need the trait so that its methods work, you can write `use std::fmt::Write as _;`.

##### 🏗️ 4.5. The main scenario: teach your own struct to print via `{}`

Remember that `{}` doesn’t work for every type? `Vec` doesn’t have it. And your struct won’t have it either, until you describe **how to show it to people**. This is exactly where `write!` is needed most often.

```Rust
use std::fmt;

struct Point {
    x: i32,
    y: i32,
}

impl fmt::Display for Point {
    fn fmt(&self, f: &mut fmt::Formatter) -> fmt::Result {
        write!(f, "({}, {})", self.x, self.y)
    }
}

fn main() {
    let p = Point { x: 3, y: 5 };

    println!("Point: {p}");              // Point: (3, 5)
    let text = format!("We are here: {p}");
    println!("{text}");                  // We are here: (3, 5)
}
```

##### 🔍 Breaking it down

- **`impl fmt::Display for Point`**: “I’m describing how to show `Point` via `{}`”. `Display` is that very “view for people”.
- **`fn fmt(&self, f: &mut fmt::Formatter)`**: this is the name of the method Rust calls when it meets `{}` with your type. `&self` is a reference to the point itself (remember borrowing?). `f` is a mutable reference to the **“sheet of paper”** on which the text is currently being assembled.
- **`write!(f, "({}, {})", self.x, self.y)`**: we write onto that sheet. The first argument is `f`, that is, “where to write”. The rest is an ordinary template.
- **No semicolon at the end**: the result of `write!` is exactly what the function returns. The type `fmt::Result` is that same “did it work or not”.

After this, `Point` can do everything: it works in `println!("{p}")`, in `format!("{p}")`, and even gets the `p.to_string()` method for free.

>  **Nuance:** width and alignment (`{p:>10}`) **won’t work on their own** for your type as long as you just use `write!` inside `fmt`. To make them work you need to use `f.pad(...)`, but that’s a topic for an advanced note.

##### 🤔 4.6. What to use when

- **You just need a new string**: `format!`.
- **You’re building a long text piece by piece** (in a loop, in several places): `write!` or `writeln!` into a `String` (`std::fmt::Write`).
- **You’re writing into a file or the console**: `write!` or `writeln!` (`std::io::Write`).
- **You’re describing how to show your struct**: `write!(f, ...)` inside `impl Display`.

---

#### 🧠 Quick cheat sheet

**Basics**

1. `format!(template, data)` returns a **new** `String`, prints nothing to the screen.
2. `write!(where, template, data)` **appends** to the specified place and returns a `Result`.
3. `writeln!` is the same as `write!`, but with a line break at the end.
4. The template in `format!`, `write!`, `println!` is **the same**.
5. Macros don’t take variables, they only look at them, so the variables stay alive.

**Holes**

6. `{name}` by variable name, `{}` by order (values after the comma), `{0}` by index, `{x}` with `x = value` after the comma.
7. Inside `{}` only a name, no expressions (`a + b`).
8. The braces themselves are printed by doubling: `{{` and `}}`.

**Formatting rules** `{:[fill][alignment][sign][#][0][width][.precision][type]}`

9. `{}` for people, `{:?}` debug view, `{:#?}` debug with indentation. Your own Debug comes from `#[derive(Debug)]`.
10. Width: `{:8}`. Alignment: `<` left, `^` center, `>` right. The fill goes before it: `{:*^20}`.
11. Precision: `{:.2}` (two digits after the decimal point), `{:08.3}` (with zeros on the left).
12. Sign: `{:+}`. Zeros: `{:05}`.
13. Number systems: `x` (hex), `X` (HEX), `b` (binary), `o` (octal), `e` (scientific notation). `#` adds a prefix: `{:#x}` gives `0xff`.
14. Width and precision from a variable: `{:>width$.prec$}`.

**`write!`**

15. For a `String` you need the import **`use std::fmt::Write;`**, and `mut` on the string.
16. For a file and the console you need **`use std::io::Write;`**, which is a different trait.
17. The result of `write!` can’t be ignored: `.unwrap()` (safe for strings), `?` (in a function that returns a `Result`), or `let _ =`.
18. `write!` in a loop is better than gluing `format!` calls together: no extra temporary strings are created.
19. Your own `Display`: `impl fmt::Display for Type`, inside it `write!(f, ...)` **with no semicolon** at the end.
20. Error about a missing `write_fmt` method? You forgot to import `Write`. Error “doesn’t implement `Display`”? Use `{:?}` or write your own `Display`.
