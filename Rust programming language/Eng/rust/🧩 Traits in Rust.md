**Core Idea:** A trait is a contract that defines shared behavior for different types. It is analogous to interfaces in other languages.

1. Basic Example (Behavior Contract)

```rust
// 1. Declare a trait (contract)
trait Speak {
    fn speak(&self);
}

struct Dog;
struct Cat;

// 2. Implement the trait for Dog
impl Speak for Dog {
    fn speak(&self) {
        println!("Woof!");
    }
}

// 3. Implement the trait for Cat
impl Speak for Cat {
    fn speak(&self) {
        println!("Meow!");
    }
}

fn main() {
    let dog = Dog;
    let cat = Cat;

    dog.speak(); // Output: Woof!
    cat.speak(); // Output: Meow!
}
```

2. Default Implementation

A trait can provide default logic for its methods. If a type does not override it, the default implementation will be used:

```rust
trait Summary {
    // Required method
    fn author(&self) -> String;

    // Method with a default implementation
    fn summarize(&self) -> String {
        format!("(Read more by {})", self.author())
    }
}

struct Article {
    author: String,
    title: String,
}

impl Summary for Article {
    fn author(&self) -> String {
        self.author.clone()
    }
    // No need to implement summarize(), the default one will work!
}

fn main() {
    let article = Article {
        author: String::from("John"),
        title: String::from("Rust Concurrency"),
    };

    println!("{}", article.summarize()); 
    // Output: (Read more by John)
}
```

3. Trait Bounds (Restricting Types in Functions)

We can accept **any** type in a function, as long as it implements the required trait.

```rust
trait Render {
    fn render_ui(&self);
}

struct CpuWidget;
struct DiskWidget;

impl Render for CpuWidget {
    fn render_ui(&self) { println!("[CPU] Status: OK"); }
}

impl Render for DiskWidget {
    fn render_ui(&self) { println!("[DISK] Status: OK"); }
}

// Accepts ANY type that implements the Render trait
fn draw_component(item: &impl Render) {
    item.render_ui();
}

fn main() {
    let cpu = CpuWidget;
    let disk = DiskWidget;

    draw_component(&cpu);
    draw_component(&disk);
}
```

4. Built-in System Traits (Letting the Compiler Do the Work)

You don't need to manually write basic operations-just use `#[derive(...)]`:

```rust
#[derive(Debug, Clone, PartialEq)]
struct Config {
    threads: u32,
    verbose: bool,
}

fn main() {
    let cfg1 = Config { threads: 12, verbose: true };
    let cfg2 = cfg1.clone(); // Works thanks to Clone

    println!("{:?}", cfg1); // Works thanks to Debug

    if cfg1 == cfg2 { // Works thanks to PartialEq
        println!("Configs are identical!");
    }
}
```

💡 Quick Cheat Sheet

- **`trait Name { ... }`** - declare an interface.
- **`impl Name for Type { ... }`** - implement the interface for a struct.
- **`impl TraitName` in arguments** - accept any struct that implements this trait.
    
- **`#[derive(Debug, Clone, PartialEq)]`** - automatically generate standard traits via the compiler.
