## **What is a buffer**

A buffer is a temporary storage in RAM (in our case, a regular `String`), where you add text step by step before displaying it on the screen.

Imagine that you are packing a box of things: instead of running to the post office with each individual T-shirt (`println!`), you first put everything in one box (`buf`), seal it and take it all at once (`print!`).

**Code Examples**

**1. Easy buffer recording and transfer**



```Rust
use std::fmt::Write;

fn main() {
// 1. Create a buffer in memory
let mut buf = String::with_capacity(512);

// 2. Fill it through functions
add_user_info(&mut buf, "MBKCHEL");
add_system_status(&mut buf, true);

// 3. Output all contents once
print!("{}", buf);
}

fn add_user_info(buf: &mut String, username: &str) {
// Write directly to a string without outputting to the console
let _ = writeln!(buf, "User: {}", username);
}

fn add_system_status(buf: &mut String, is_active: bool) {
let status = if is_active { "Working" } else { "Disabled" };
let _ = writeln!(buf, "System status: {}", status);
}
```

**2. Accumulating data in a loop (for example, a list of processes)**



```Rust
use std::fmt::Write;

fn append_processes(buf: &mut String) {
    let processes = vec![
        ("sysprint", "12 MB"),
        ("hyprland", "140 MB"),
        ("kitty", "45 MB"),
    ];

    let _ = writeln!(buf, "--- List of processes ---");
    for (name, ram) in processes {
        // Each circle of the loop adds a new line to the buffer
        let _ = writeln!(buf, "Software: {:<10} | RAM: {}", name, ram);
    }
}

```

**Pros and cons of using buffers**

**Pros**

- **Huge speed:** Displaying text on the screen (I/O) is a very slow operation. Writing 100 lines into memory and one `print!` is tens of times faster than 100 separate `println!`.

- **Ease of processing:** When all the information is collected in one `String` variable, it is easy to split it into lines (`buf.lines()`), calculate the length, align it or paste it next to the ASCII logo.

- **Saving CPU resources:** The processor is not distracted by constant context switching to work with the terminal.


**Cons**

- **RAM consumption:** Text temporarily takes up space in RAM until it is output and cleared.

- **Lack of real time:** If the program takes too long to collect data, the user will not see anything in the console until the entire buffer is completely filled and printed.

- **Additional code:** We have to pass a `&mut String` reference through function arguments and handle macros like `writeln!`.
