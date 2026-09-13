### 1. What is it?

A regular array `[i32; 5]` in Rust has a fixed size on the heap/stack - it cannot be increased. **Vector (`Vec<T>`)** is a dynamic array. It stores elements in a heap (Heap) and can expand and contract on the fly.

### 2. Methods of creation



```Rust
// 1. Empty vector via constructor
let mut v: Vec<i32> = Vec::new();

// 2. Through the vec macro! (immediately with elements)
let v2 = vec![1, 2, 3];

// 3. With preliminary memory allocation (for optimization, if you know the size)
let mut v3 = Vec::with_capacity(100);
```

### 3. Adding and removing elements



```Rust
let mut pool: Vec<char> = Vec::new();

// Add 1 element to the end
pool.push('a');

// Add a bunch of elements from the iterator
pool.extend("bcde".chars());

// Remove last element (returns Option<T>)
let last = pool.pop(); //Some('e')
```

### 4. Accessing elements



```Rust
let numbers = vec![10, 20, 30];

// Method A: Through indexes (DANGER! If there is no index, the program will crash/panic)
let first = numbers[0];

// Method B: Via .get() (SAFE! Returns Option<&T>)
match numbers.get(0) {
Some(val) => println!("Number: {}", val),
None => println!("There is no element!"),
}
```

### 5. Looping through a vector (Iteration)



```Rust
let items = vec!["Rust", "C++", "Python"];

// Read without changing elements
for item in &items {
println!("{}", item);
}

// Changing elements in place
let mut numbers = vec![1, 2, 3];
for num in &mut numbers {
*num *= 2; // Multiply each number by 2
}
```

### 6. Reading vector elements
``` Rust
years in = all!yu1, 2, 3, 4, 5sch;

let third: &i32 = &v[2]; // Gave a reference to the 2nd element of the vector v (0, 1, 2)
println!("The third element is: {third}"); // Prints 3

// The .get() method safely returns the element at index as an `Option` enum
match v.get(2){
Some(third) => println!("The third element is: {third}"),
None => println!("The third element is missing"),
}
```

### 7. Common methods that save lives

- `.len()` - current number of elements.

- `.get()` - The `.get()` method of vectors safely returns the element at index as an `Option` enumeration

- `.is_empty()` - checks whether the vector is empty (`true`/`false`).

- `.contains(&element)` - checks if there is an element inside.

- `.clear()` - clears the vector completely.

- `.choose(&mut rng)` _(from crate `rand`)_ - gives a random element.
