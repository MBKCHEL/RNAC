**Core Concept:** Multithreading allows you to assign work to a team of workers instead of just one, fully utilizing all CPU cores and completing tasks exponentially faster.

---

### 1. Rayon: Single-Line Data Parallelism

> **What it is:** A crate that automatically splits a large array, vector, or range into chunks and distributes them across all available CPU cores using work-stealing algorithms.

* **Analogy:** Instead of one person washing 100 dishes sequentially, 12 people step in and wash their own stack simultaneously.

```rust
use rayon::prelude::*; // Brings Rayon traits into scope

fn main() {
    // .into_par_iter() turns a standard iterator into a parallel one
    let sum: u128 = (1..1_000_000_000_u128)
        .into_par_iter() // <- ALL THE MAGIC HAPPENS HERE
        .map(|n| n * 2)   // Executed concurrently on all CPU cores
        .sum();

    println!("Sum: {}", sum);
}
```

* **Key Benefit:** Zero manual thread management or lifetime juggling.
* **When to use:** Vectors, arrays, heavy loops, parsing multiple files in parallel.

---

### 2. `std::thread::spawn`: Spawning OS Threads

> **What it is:** Low-level thread creation via Rust's standard library to execute tasks on a separate native OS thread.

```rust
use std::thread;
use std::time::Duration;

fn main() {
    // Spawn creates a new background worker thread
    let handle = thread::spawn(|| {
        thread::sleep(Duration::from_millis(500)); // Pause (thread sleeps)
        println!("Background thread finished work!");
    });

    println!("Main thread continues executing...");

    // join() blocks the main thread until the spawned thread completes
    handle.join().unwrap(); 
}
```

* **`thread::sleep(...)`** - Suspends the thread so it doesn't wastefully consume 100% of CPU cycles.
* **`handle.join()`** - Waits for the thread to finish. Without this, `main` might terminate before the child thread gets a chance to run.

---
### 3. ### Real-World Example: Running Heavy Functions in Threads

Imagine these functions read system files containing system information (like in `/proc` or `/sys`):

```rust
use std::thread;
use std::time::Duration;

// Heavy function 1: Parsing system logs
fn parse_system_logs() -> usize {
    thread::sleep(Duration::from_millis(300)); // Simulating long computation
    42 // Return number of errors found
}

// Heavy function 2: Calculating CPU load
fn calculate_cpu_load() -> f32 {
    thread::sleep(Duration::from_millis(200));
    14.8 // Return CPU load percentage
}

// Heavy function 3: Scanning disk space
fn scan_disk_space() -> u64 {
    thread::sleep(Duration::from_millis(500));
    1_024_000_000 // Return free bytes
}

fn main() {
    println!("🚀 Launching tasks in parallel threads...");

    // Spawn each function in its own background thread
    let handle_logs = thread::spawn(|| {
        parse_system_logs() // Calling a standard function inside the closure
    });

    let handle_cpu = thread::spawn(|| {
        calculate_cpu_load()
    });

    let handle_disk = thread::spawn(|| {
        scan_disk_space()
    });

    // Retrieve results from threads
    // .join().unwrap() blocks main until the thread returns its value
    let log_errors = handle_logs.join().unwrap();
    let cpu_load = handle_cpu.join().unwrap();
    let free_space = handle_disk.join().unwrap();

    println!("\n✅ All tasks completed!");
    println!("Log errors found: {}", log_errors);
    println!("CPU Load: {}%", cpu_load);
    println!("Free disk space: {} bytes", free_space);
}
### 4. Transferring Data Between Threads

Threads live in isolation. To share or send data, use these three primary mechanisms:

#### A. The `move` Keyword (Transferring Ownership)
If a thread closure uses variables from its outer scope, it must take full ownership of them.

```rust
let data = vec![1, 2, 3];

// move transfers ownership of `data` into the thread closure
thread::spawn(move || {
    println!("{:?}", data);
});
```
#### Why this example matters:

- **Parallelism:** If you executed these functions sequentially in a single thread, it would take $300 + 200 + 500 = 1000\text{ ms}$ (1 second). Running them concurrently in parallel threads reduces total runtime to the longest single thread — exactly $500\text{ ms}$!
    
- **Returning Values:** The functions return standard Rust types (`usize`, `f32`, `u64`). The closures in `thread::spawn` pass these values through, and `.join().unwrap()` unwraps them directly into variables within the main thread.
#### B. `mpsc` Channels (Message Passing)
Follows the pattern: *"Do not communicate by sharing memory; instead, share memory by communicating."*

`mpsc` stands for **Multiple Producer, Single Consumer**.

```rust
use std::sync::mpsc;
use std::thread;

fn main() {
    let (tx, rx) = mpsc::channel();
    let x = "21";

    // Spawn a thread
    let handle = thread::spawn({
        // Clone the transmitter specifically for this thread
        let tx = tx.clone();
        move || {
            tx.send("Data from thread").unwrap();
            println!("Inside thread, x = {}", x);
        }
    });

    // Send a message from the main thread
    tx.send(x).unwrap();

    // CRITICAL: Drop the original transmitter in main.
    // Only the cloned `tx` inside the thread remains alive.
    // When that thread exits, the channel closes completely.
    drop(tx);

    // Iterating over `rx` automatically waits and consumes ALL messages
    // until all transmitters are dropped.
    for message in rx {
        println!("Received: {}", message);
    }

    handle.join().unwrap();
}
```

#### C. `Arc<Mutex<T>>` (Shared Mutable State)
When multiple threads need to safely read and mutate the **same** data in memory:

* **`Mutex<T>`** (*Mutual Exclusion*) - A safe with a lock. Only one thread can access the inner data at a time.
* **`Arc<T>`** (*Atomic Reference Counting*) - Thread-safe smart pointer that allows multiple threads to own access to the same `Mutex`.

```rust
use std::sync::{Arc, Mutex};
use std::thread;

fn main() {
    let counter = Arc::new(Mutex::new(0)); // A safe initialized with 0
    let counter_clone = Arc::clone(&counter);

    thread::spawn(move || {
        let mut num = counter_clone.lock().unwrap(); // Acquire lock
        *num += 1;                                    // Mutate value
    }); // Lock is automatically released here as `num` goes out of scope
}
```

---

### 💡 Quick Mental Rules

* **Standard Iterator:** `.into_iter()` → Single-threaded (1 core).
* **Parallel Iterator:** `.into_par_iter()` → Multi-threaded (All cores).
* **`Send`:** Marker trait indicating a type can be transferred across thread boundaries.
* **`Sync`:** Marker trait indicating a type can be referenced concurrently by multiple threads (`&T`).
* **Data Races:** Impossible in safe Rust due to ownership and borrowing rules enforced at compile time.
