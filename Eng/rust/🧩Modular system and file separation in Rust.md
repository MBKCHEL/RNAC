In Rust, everything is built around the **Crate** and the **Tree of Modules**. The root of the tree is always the file `src/main.rs` (for binaries) or `src/lib.rs` (for libraries).

## 1. The main rule of dividing into files

> **In Rust, a file by itself does NOT automatically become a module.**
>
> For a file to become a module, it **MUST** be declared in the parent file using the `mod` keyword.

## 2. File structure options

Let's imagine that we want to move the logic for working with the CPU into a separate `cpu` module.

### Option A: Single file (for small modules)

1. Create a file: `src/cpu.rs`

2. In the code `src/cpu.rs`:



```Rust
pub fn print_info() {
println!("CPU Info");
}
```

3. We declare it in `src/main.rs`:



```Rust
mod cpu; // Rust looks for either src/cpu.rs or src/cpu/mod.rs

fn main() {
cpu::print_info();
}
```


### Option B: Folder with submodules (for complex components)

If the module grows (like yours with `sysinfo` in SysPrint), it is more convenient to split it into a folder with subfiles:

**Folder structure:**



``` r
src/
├── main.rs
└── sysinfo/
├── mod.rs <-- Main module file
├── cpu.rs <-- Submodule
└── memory.rs <-- Submodule
```

1. In `src/sysinfo/cpu.rs`:



```Rust
pub fn get_usage() -> f32 { 15.4 }
```

2. In `src/sysinfo/mod.rs` (declare submodules):



```Rust
pub mod cpu;    // Declare sysinfo/cpu.rs
pub mod memory; // Declare sysinfo/memory.rs
```

3. In `src/main.rs` (we connect the top-level module):



```Rust
mod sysinfo;

fn main() {
let usage = sysinfo::cpu::get_usage();
}
```


## 3. Publicity (`pub`) and visibility

By default, in Rust **everything is private** (functions, structures, and modules).

- `pub fn`: makes the function available from outside the module.

- `pub mod`: makes the module accessible from outside the parent module.

- `pub struct MyStruct`: makes the structure itself public, **BUT its fields remain private**! For fields to be visible, they also need `pub`:



```Rust
pub struct Config {
pub enable_color: bool, // Public field
secret_key: String, // Private field
}
```


## 4. Shortcuts: `use` and `pub use` (Re-export)

### Keyword `use`

In order not to write the long path `crate::sysinfo::cpu::get_usage()` every time, we import it:



```Rust
use sysinfo::cpu;

fn main() {
cpu::get_usage();
}
```

### Paths: `crate::`, `super::`, `self::`

- `crate::` - path from the project root (`src/main.rs`).

- `super::` - path from the parent module (like `..` in the terminal).

- `self::` - path from the current module.


### `pub use` (Re-exporting)

A very powerful feature for the convenience of users of your library/module. Allows you to make the internal folder structure hidden by pulling the necessary functions to the top.

In `src/sysinfo/mod.rs`:



```Rust
mod cpu; // the file itself is private

pub use cpu::get_usage; // pull the function directly into sysinfo
```

Now in `main.rs` you can simply call `sysinfo::get_usage()`, without knowing which internal file it is in!

## Cheat sheet for the checklist:

1. Created a file `src/something.rs` $\rightarrow$, don’t forget to write `mod something;` in `main.rs`.

2. If you don’t see a function from another file $\rightarrow$, check if `pub` comes before `fn` and before `mod`.

3. If you want a nice clean `main.rs` $\rightarrow$, use `use crate::module::submodule;`.
