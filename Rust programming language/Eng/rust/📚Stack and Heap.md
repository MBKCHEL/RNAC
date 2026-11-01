Both of these things are pieces of random access memory (RAM), but Rust uses them differently for speed and security.


**Stack**                 **Heap**
**Fast (LIFO)**           **Slower (search for location)**
**Fixed size**            **Dynamic size**
**Stores simple types**   **Stores complex/growing data**

### 1. Stack - “Stack of plates”

It works on the **LIFO** principle (_Last In, First Out_ - last to come, first to leave). Like a stack of plates: you put it on top, you take it off from the top too.

- **Main feature:** Memory is allocated instantly because the compiler **knows in advance the exact size** of the data.

- **What lies there:**

- Simple fixed-size types: `i32`, `f64`, `bool`, `char`.

- Fixed length arrays: `[i32; 5]`.

- Pointers/references (pointing to data on the heap).

- **Behavior when calling functions:** When a function is executed, its variables are pushed onto the stack. The function has ended - its entire frame is immediately reset from the stack.


### 2. Heap - “Wardrobe with numbers”

A large chaotic memory warehouse. The data sizes there may grow or be unknown at compile time.

- **How it works:**

1. The program asks the operating system: _"Give me 100 bytes"_.

2. The OS looks for free space, places data there and **returns the address (pointer)**.

3. This pointer (address) is stored **on the Stack**.

- **What lies there:**

- Dynamic strings: `String::from("hello")`.

- Vectors: `Vec<T>` (lists that can grow).

- Complex dynamic objects.


### 3. How is Ownership related to them?

This is where the magic of Rust brings everything together:

1. When you write `let s = String::from("hello");`:

- Memory is allocated in the **Heap** for the letters `"hello"`.

- In the **Stack**, a structure `s` is created, which contains: **pointer** to the heap, **length** (`len`) and **capacity** (`capacity`).

2. When `s` goes out of scope (closes bracket `}`):

- Rust automatically calls the `drop` function.

- Memory in the **heap** is instantly cleared. No leaks!


### 💡 A short cheat sheet to remember:

|**Parameter**|**Stack**|**Heap**|
|---|---|---|
|**Speed**|🚀 Very Fast|🐢 Slower|
|**Data size**|Fixed at compilation|Can change on the fly|
|**Access**|Directly|By index (link)|
|**Examples**|`i32`, `bool`, `&str`|`String`, `Vec<T>`|
