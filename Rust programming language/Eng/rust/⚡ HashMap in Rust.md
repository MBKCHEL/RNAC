`HashMap<K, V>` is an associative array (hash table) from `std::collections` that stores data in the form of **"Key (`K`) -> Value (`V`)"** pairs in dynamic memory (heap).

**Why is it needed?**

- **Instant access:** Search for an element by string, ID or unique value in $O(1)$ without brute force.

- **Aggregation:** Counting quantities, grouping data, storing configurations and cache.


### ⚖️ Pros and Cons

|**Pros ➕**|**Cons ➖**|
|---|---|
|**High speed ($O(1)$):** Search, insert and delete work in constant time.|**Heap memory allocation:** Works slower than stack arrays due to allocations.|
|**Convenient API (`entry`):** Safe modification and insertion of data without double searching.|**Chaotic order:** Elements do not preserve the order they are added.|
|**Strong typing:** Full compiler control over key and value types.|**Key requirements:** The `K` key must implement the `Eq` + `Hash` traits.|

### 🛠 Basic CRUD (Syntax)

To work, you always need an import: `use std::collections::HashMap;`

**1. Creation**



```Rust
// Empty map with automatic type inference
let mut map: HashMap<&str, i32> = HashMap::new();

// With pre-allocated memory for 100 elements (optimization)
let mut fast_map = HashMap::with_capacity(100);
```

**2. Add and Update (`insert`)**



```Rust
let mut map = HashMap::new();

// Insert a new pair
map.insert("Arch", 100);

// Overwrite: if the "Arch" key is present, return the old value (Some(100)) and write 200
let old_val = map.insert("Arch", 200);
```

**3. Read (`get`)**

Returns `Option<&V>` since the key may not be in the map.



```Rust
let mut map = HashMap::new();
map.insert("SysPrint", 11);

// Option 1: Safe extraction via if let
if let Some(stars) = map.get("SysPrint") {
println!("Stars: {stars}");
}

// Option 2: Get the value or take the default one
let stars = map.get("SysPrint").copied().unwrap_or(0);
```

**4. Check and Remove (`contains_key`, `remove`)**



```Rust
let mut map = HashMap::new();
map.insert("AX200", "WiFi");

// Check availability
if map.contains_key("AX200") {
// Delete (returns Option<V> with the value removed)
let removed = map.remove("AX200");
}
```

### ⚡ Atomic operation via `.entry()` API

Eliminates the need to manually check “is there a key, if not, add it.”



```Rust
use std::collections::HashMap;

fn main() {
    let mut stats = HashMap::new();

    // 1. Insert 100 ONLY if the "stars" key IS NOT YET
    stats.entry("stars").or_insert(100);
    stats.entry("stars").or_insert(500); // Ignored since the key already exists

    // 2. Use as a counter
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

### ⚠️ Ownership of Memory

- **Types with `Copy`** (`i32`, `f64`, `bool`): Copied to the card, the originals remain available.

- **Owned types** (`String`, `Vec<T>`): **Move** inside the map!




```Rust
let key = String::from("GPU");
let val = String::from("RTX 4060 Ti");

let mut hardware = HashMap::new();
hardware.insert(key, val);

// println!("{key}"); // ❌ Error! The key variable has been moved to the HashMap.
```

> **Solution:** Use `HashMap<&str, &str>` borrow or pass `.clone()`.

### 🖥 Output HashMap to console



```Rust
use std::collections::HashMap;

fn main() {
let mut map = HashMap::new();
map.insert("Rust", "1.80");
map.insert("Kernel", "6.10");

// 1. Debug output of the entire card (in one line)
println!("{:?}", map);

// 2. Formatted multi-line debug output
println!("{:#?}", map);

// 3. Output in a loop (be sure to use &map so as not to burn the property!)
for (key, val) in map {
println!("{key} => {val}");
}
// 4. Output specifically by key

let mut map_1 = HashMap::new();
map_1.insert(10, "Intel AX200");

// Option 1: Via unwrap_or (if there is no key, it will display the default text)
println!("Network: {}", map_1.get(&10).unwrap_or(&"netu"));
```

### 📌 Cheat sheet

- `use std::collections::HashMap;` - import.

- `.insert(k, v)` - add / overwrite.

- `.get(&k)` - read (`Option<&V>`).

- `.entry(k).or_insert(v)` - safe to update/initialize.

- `.remove(&k)` - remove.

- `for (k, v) in &map` - iterate without losing ownership.
