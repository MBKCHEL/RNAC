### 1. `struct` is a Drawing (Template)

A structure doesn't create specific data, it just tells Rust what an object is made of.



```Rust
#[derive(Debug)] // <-- Magic: allows you to print the entire structure via {:?}
struct User {
name: String,
email: String,
age: u32,
active: bool
}
```

### 2. Creating objects and chips (Shorthand & Update)

#### 🔹 Field Init Shorthand

If the variable names match the fields of the structure, there is no need to duplicate:



``` rust
let name = String::from("MBKCHEL");
let email = String::from("mbkchelofficialdev@gmail.com");
```

``` rust
// Instead of name: name we write simply name
let user1 = User {
name,
email,
age: 13,
active: true,
};
```

#### 🔹 Create from another object (Struct Update Syntax)

Do you want to copy the user, but change only a couple of fields? Use `..`:



``` rust
let user2 = User {
name: String::from("BALBES"),
email: String::from("yourexample@gmail.com"),
..user1 // All other fields (age, active) will be taken from user1!
};
```

### 3. `impl` (Implementation) - Give the structure “Superpowers”

Without `impl` the structure is just a bag of data. With `impl` we write **methods** (functions that belong to this structure).



```Rust
impl User {
    // 1. Constructor (Associated function) - creates a new user
    fn new(name: String, email: String) -> Self {
        Self {
            name,
            email,
            age: 13,
            active: true,
        }
    }

    // 2. Read method (&self) - just looks at the data
    fn print_info(&self) {
        println!("User: {}, Email: {}", self.name, self.email);
    }

    // 3. Change method (&mut self) - can change fields!
    fn make_older(&mut self) {
        self.age += 1;
        println!(
            "Happy birthday, {}! You are {} years old now.",
            self.name, self.age
        );
    }
}

```

### 4. Tuple Structs

If you need a structure, but are too lazy to come up with names for the fields:



```Rust
struct Color(i32, i32, i32); // RGB
struct Point(i32, i32, i32); // X, Y, Z coordinates

let black = Color(0, 0, 0);
println!("Red color: {}", black.0); // Access via index!
```


### 5. Unit-Like Structs
## Structures without a single field. Occupies 0 bytes in memory.

```rust
struct AlwaysEqual;

let subject = AlwaysEqual;
```

### **Why are they needed?** Empty structures are used when you don’t need to store data, but you need to attach some behavior to the structure via `impl` or implement a **Trate** (interface). For example, to create services, parsers or type markers.

### 🚀 Full working example in one block:



```Rust
#[derive(Debug)]
struct Player {
    nickname: String,
    hp: u32,
}

impl Player {
    fn new(nickname: String) -> Self {
        Self { nickname, hp: 100 }
    }

    fn take_damage(&mut self, damage: u32) {
        self.hp -= damage;
        println!(
            "{} took {} damage! HP remaining: {}",
            self.nickname, damage, self.hp
        );
    }
}

fn main() {
    let mut player1 = Player::new(String::from("MBKCHEL"));
    player1.take_damage(20);

    println!("{:#?}", player1); // Outputs the entire structure beautifully!
}

```
### 6. Ownership of `self` in methods (3 types of data reception)

In Rust, a method chooses how to work with an object:

1. `&self` - **Reading** (Used in 90% of cases). Does not take possession, cannot change data.
2. `&mut self` - **Mutation**. Allows you to change structure fields. Requires that the instance itself be declared using `let mut`.
3. `self` - **Take possession (Move)**. The object is **destroyed** inside the method or transformed into something else. After the call, the original variable dies!

```rust
impl Player {
    // Takes self by value - eats the object
    fn die(self) {
        println!("Player {} is dead forever.", self.nickname);
        // self goes out of scope and is cleared from memory
    }
}

```
### 7. Multiple `impl` blocks

In Rust, you don't have to cram all methods into one `impl` block. They can be divided into several blocks (convenient for large files or organizing code):



```Rust
impl Player {
    fn new(nickname: String) -> Self {
        Self { nickname, hp: 100 }
    }
}

// Second impl block for the same structure
impl Player {
    fn heal(&mut self, amount: u32) {
        self.hp += amount;
    }
}

```
### 8. Methods with multiple parameters

The method can take any additional arguments, just like regular functions:



```Rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // Checks if another rectangle will fit inside the current one
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    let rect1 = Rectangle {width: 30,height: 50,};
    let rect2 = Rectangle {width: 10, height: 40,};

    println!("Will it fit? {}", rect1.can_hold(&rect2)); // true
}

```

### 💡 Checklist to remember

- **`struct`** - stores only **data**.

- **`impl`** - stores only **behaviour** (functions and methods).

- Call via **`.`** (`rect.area()`) - for methods with `self`.

- Call via **`::`** (`String::from()`, `Player::new()`) - for associated functions without `self` (constructors).
