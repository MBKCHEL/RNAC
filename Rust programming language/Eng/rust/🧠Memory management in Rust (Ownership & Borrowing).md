Unlike C# (Unity) or JS, where memory is monitored by a lazy Garbage Collector, in Rust memory is managed by **YOU** through an ironclad set of rules. And the logic here is the same as in the times of ancient Rome.

## 👑 1. Rule of Ownership: One Slave - One Master

In Rust, in-memory data (such as a heavy `String`) is a **slave**. And the variable is its **“official owner”**.

> **THE MAIN LAW OF GROWTH:** One “slave” at one time can have **only one** master. No co-ownership!

### 🚚 Transfer of ownership permanently (`Move`)

If you pass a variable to another function without any signs, you are simply rewriting ownership documents.



```Rust
fn main() {
    let s1 = String::from("Memory Slave"); // s1 - first owner

    throw_to_method(s1); // THE DOCUMENTS HAVE BEEN SUBMITTED! (Move)

    // println!("{s1}"); // ERROR! s1 is no longer the owner, she has no rights to this data!
}

fn throw_to_method(s2: String) {
    // Now s2 is the full and only owner of the "Memory Slave"
    println!("{s2}");
} // The function has ended. s2 dies, and with it the memory is CLEARED!
```

## 🤝 2. Borrowing: Let the kent “drive”

To prevent the variable from dying when passed to methods, you use **Borrow**. You don't hand over documents for a slave, you just put an ampersand **`&`**. This means: “Here, Kent, take my slave, let him do the work, but I remain the master.”_.



```Rust
fn main() {
    let s1 = String::from("Memory Slave");

    dat_pogonat_brotu(&s1); // Passed the LINK (&). The documents remain with s1!
    eshe_one_method(&s1);   // No problem, let a second friend drive!

    println!("{s1} still serves me in main!"); // WORKS! The variable is alive!
}

fn dat_pogonat_brotu(rab: &String) {
    println!("Bro uses: {rab}");
}

fn eshe_one_method(rab: &String) {
    println!("Another bro checks: {rab}");
}
```
## 🎭 3. Two types of links (How to give “drive”)

There are two strict rules here so that the bros don’t fight and ruin your data:

### 👁️ Option A: Reading link (`&s`) - “View only”

You can give a “watch only” link to **at least 100 kents at the same time**. They can read the data, but have no right to change it.



```Rust
let r1 = &s1; // OK
let r2 = &s1; // OK (everyone is just watching)
```

### 🪓 Option B: Mutable link (`&mut s`) - “Let it work / change”

If a client wants to change the data (for example, add text to a line), you give a mutable link `&mut`.

> **BUT:** You can give it to **only ONE kent at a time**. While one is working with an ax (`&mut`), no one else even has the right to peek (`&`), so that bugs do not happen.



```Rust
fn main() {
    let mut s = String::from("Sword ");

    sharpen_sword(&mut s); // Passed a mutable link

    println!("Now we have: {s}"); // Prints: "Now we have: Sword sharpened!"
}

fn sharpen_sword(item: &mut String) {
    item.push_str("sharpened!"); // Changed data inside someone else's variable!
}
```

## 🐑 4. Cloning (`.clone()`): Make an exact copy

If you need a bloody nose and want to keep the slave yourself and give **your own** slave to a friend, you do cloning. A duplicate is created in memory. This is more expensive in terms of resources, but sometimes necessary.



```Rust
fn main() {
    let s1 = String::from("Grenade");
    let s2 = s1.clone(); // Created a second EXACTLY THE SAME slave in memory

    // Now s1 has his own slave, s2 has his own. Nobody depends on anyone!
    println!("{s1} and {s2} exist separately!");
}
```

### 🧠 Short cheat sheet:

1. Transferred as is (`s`) - **gave away the documents for good**, the variable in `main` died.

2. Passed with an ampersand (`&s`) - **let the guy run**, the variable is alive.

3. Sent with a mutable ampersand (`&mut s`) - **gave the kent to rewrite the data**, but at this moment there should be only ONE kent.

4. Called `.clone()` - **created a duplicate in memory**.
