- **`cargo new project_name`** - creates a new project from scratch. Immediately cuts the finished folder structure, the `src/main.rs` file and, most importantly, `Cargo.toml`, where all the dependencies are written.

- **`cargo build`** - builds (compiles) your project. The finished `.exe` file appears in the `target/debug` folder.

- **`cargo run`** - compiles and immediately runs the code. The most popular command during tests: pressed - checked.

- **`cargo check`** - super-fast code checking for errors. It doesn't build a binary, so it works instantly. It’s convenient to run it to find out whether the compiler complains about your syntax or not, without wasting time on the full assembly.
- **`cargo buid --release`** - compiles the project, but for release. Compilation will take longer, but the project will be optimized as much as possible.



- **`rustup update`** is a command for updating the compiler language. (Enter into console)

- **`rustup doc`** - this command opens local documentation in an offline browser. (Enter into console)
