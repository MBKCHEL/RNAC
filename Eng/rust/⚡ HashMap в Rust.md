# ⚡ HashMap in Rust

`HashMap<K, V>` is an associative array (hash table) from `std::collections` that stores data as **"Key (`K`) -> Value (`V`)"** pairs in dynamic memory (heap).

**Why you need it:**
* **Instant access:** Search for an element by string, ID, or unique value in $O(1)$ time without full traversal.
* **Aggregation:** Counting quantities, grouping data, storing configurations and cache.

## ⚖️ Pros and Cons

| **Pros ➕** | **Cons ➖** |
| :--- | :--- |
| **High speed ($O(1)$):** Search, insertion, and deletion work in constant time. | **Heap allocation:** Works slower than stack arrays due to allocations. |
| **Convenient API (`entry`):** Safe mutation and insertion without double lookups. | **Unordered:** Elements do not preserve insertion order. |
| **Strict typing:** Full compiler control over key and value types. | **Key requirements:** Key `K` must implement `Eq` + `Hash` traits. |

## 🛠 Basic CRUD (Syntax)

Import is always required: `use std::collections::HashMap;`

### 1. Creation

```rust
// Empty map with automatic type inference
let mut map: HashMap<&str, i32> = HashMap::new();

// Pre-allocated memory for 100 elements (optimization)
let mut fast_map = HashMap::with_capacity(100);
```

**2. Insertion and Updating (`insert`)**


```Rust
let mut map = HashMap::new();

// Insert a new pair
map.insert("Arch", 100);

// Overwrite: if key "Arch" exists, returns previous value (Some(100)) and writes 200
let old_val = map.insert("Arch", 200);
```

**3. Reading (`get`)**

Returns `Option<&V>`, as the key might not exist in the map.

```Rust
let mut map = HashMap::new();
map.insert("SysPrint", 11);

// Option 1: Safe extraction via if let
if let Some(stars) = map.get("SysPrint") {
    println!("Stars: {stars}");
}

// Option 2: Get value or fallback to default
let stars = map.get("SysPrint").copied().unwrap_or(0);
```

**### 4. Checking and Removing (`contains_key`, `remove`)**



```Rust
let mut map = HashMap::new();
map.insert("AX200", "WiFi");

// Presence check
if map.contains_key("AX200") {
    // Removal (returns Option<V> with the removed value)
    let removed = map.remove("AX200");
}
```

### ⚡ Atomic Operations via .entry() API

Eliminates manual checks for "if key exists; if not, insert



```Rust
use std::collections::HashMap;

fn main() {
    let mut stats = HashMap::new();

    // 1. Insert 100 ONLY if key "stars" DOES NOT exist yet
    stats.entry("stars").or_insert(100);
    stats.entry("stars").or_insert(500); // Ignored because key already exists

    // 2. Usage as a counter
    let text = "rust arch rust linux arch rust";
    let mut counter = HashMap::new();

    for word in text.split_whitespace() {
        // or_insert() returns &mut V (mutable reference)
        let count = counter.entry(word).or_insert(0);
        *count += 1; // Dereference and increment
    }
    // Result: {"rust": 3, "arch": 2, "linux": 1}
}
```

### ⚠️ Memory Ownership

- **Types implementing `Copy`** (`i32`, `f64`, `bool`): Copied into the map; originals remain accessible.
    
- **Owned types** (`String`, `Vec<T>`): **Moved (`move`)** into the map!
    



```Rust
let key = String::from("GPU");
let val = String::from("RTX 4060 Ti");

let mut hardware = HashMap::new();
hardware.insert(key, val);

// println!("{key}"); // ❌ Error! Variable key was moved into HashMap.
```

> **Solution:** Use borrowing `HashMap<&str, &str>` or pass `.clone()`

### 🖥 Outputting HashMap to Console



```Rust
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();
    map.insert("Rust", "1.80");
    map.insert("Kernel", "6.10");

    // 1. Debug output of entire map (single line)
    println!("{:?}", map);

    // 2. Formatted multi-line debug output
    println!("{:#?}", map);

    // 3. Iterative output (must use &map to prevent moving ownership!)
    for (key, val) in &map {
        println!("{key} => {val}");
    }
    
    // 4. Output specific key
    let mut map_1 = HashMap::new();
    map_1.insert(10, "Intel AX200");

    // Option 1: Via unwrap_or (prints default text if key doesn't exist)
    println!("Network card: {}", map_1.get(&10).unwrap_or(&"none"));
}
```

### ## 📌 Cheat Sheet

- `use std::collections::HashMap;` - import.
    
- `.insert(k, v)` - insert / overwrite.
    
- `.get(&k)` - read (`Option<&V>`).
    
- `.entry(k).or_insert(v)` - safely update / initialize.
    
- `.remove(&k)` - delete.
    
- `for (k, v) in &map` - iterate without losing ownership.