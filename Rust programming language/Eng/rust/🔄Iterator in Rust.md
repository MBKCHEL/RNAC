## 🎯 What is it

**Iterator** is a “walker” through the elements of a collection (array, vector, etc.). Instead of manually counting the indices (`i = 0`, `i = 1`), you ask the iterator: _"Give me one element at a time until I run out of them"_.

- **Main feature:** In Rust, iterators are **lazy** (Zero-Cost Abstractions). They don't do anything and don't forget their memory until you explicitly ask them to run through the elements.


## 🛠 3 Ways to Get Iterator

Depending on what you need to do with the data:

1. **`iter()`** - iteration over **references** (`&T`). The original array remains intact, you just “look” at the elements.

2. **`iter_mut()`** - iteration over **mutable references** (`&mut T`). Allows you to change elements in place.

3. **`into_iter()`** - **takes possession** (`T`). The array or vector is then destroyed (moved).


> 💡 When you write `for element in help_print`, Rust automatically calls `.into_iter()` or `.iter()` under the hood.

## ⚡️ Basic Example



```Rust
fn main() {
    let music_list = ["track 1.mp3", "track2.mp3", "track 3.mp3"];

    // Simple iteration through for (auto-iterator)
    for track in music_list.iter() {
        println!("Playing: {track}");
    }
}

```

## 🏎 Cool methods (Chains)

Iterators can be combined into powerful data processing chains without a single `for` loop:

- **`.map()`** - transforms each element.

- **`.filter()`** - leaves only what matches the condition.

- **`.collect()`** - collects the result back into a new vector (`Vec`).




```Rust
let numbers = vec![1, 2, 3, 4, 5];

// Take even numbers and multiply by 10
let result: Vec<i32> = numbers
    .into_iter()
    .filter(|x| x % 2 == 0) // 2 and 4 will remain
    .map(|x| x * 10) // become 20 and 40
    .collect(); // collected into a new Vec

// result = [20, 40]

```
