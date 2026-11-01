#### 🎲 The `rand` Crate: The Wheel of Fortune

Rust has **no** built-in random number generator, it’s not even in the standard library. So all randomness comes from a crate (library) called **`rand`**. `rand` is a messenger who rolls the dice for you.

##### 📦 1. How to install it

Open a terminal in your project folder and type:

bash

```bash
cargo add rand
```

Cargo will add a line to `Cargo.toml` under `[dependencies]` for you. You can check the version there. This matters, because versions 0.9 and 0.10 renamed a lot of functions and traits, and in older tutorials you’ll see the old names.

> ⚠️ **VERSION TRAP:** `rand` often breaks old code when it updates. If you copied code from an old video and the compiler complains about `gen_range`, `thread_rng` or `Rng`, it means your version is new and the code is old.
>    Version                        What changed
> |0.8 → 0.9|`thread_rng()` → `rng()`, `gen_range()` → `random_range()`, `gen_bool()` → `random_bool()`, `gen()` → `random()`|
> |0.9 → 0.10|Trait `Rng` → `RngExt` (the `use` line changes)|
> In 0.10 the name `Rng` didn’t disappear, it now means a **different** trait (the low-level one, formerly `RngCore`). That’s why the old `use rand::Rng;` compiles with an “unused import” warning, but the methods still can’t be found.

##### 🎰 2. The simplest roll: `rand::random()`

Want just a random number without any fuss? Call `random()`:

```Rust
fn main() {
    let a: u8 = rand::random();        // random number from 0 to 255
    let b: f64 = rand::random();       // random float from 0.0 to 1.0
    let c: bool = rand::random();      // heads or tails

    println!("{a} {b} {c}");
}
```

Notice that Rust has to **know the type**, so the compiler understands what the heck you want from it. That’s why we write `let a: u8`. If you don’t specify the type, it won’t know what you’re asking for.

##### 🏺 3. Your own generator: `rand::rng()`

`random()` is handy for a single roll. But if you need **many** rolls of different kinds, you summon **your own slave** (a generator):

```Rust
use rand::RngExt;
// ^ IMPORTANT! Without this line the methods below won't appear!
// rand 0.10 and newer: RngExt
// rand 0.9 and older: use rand::Rng;

fn main() {
    let mut rng = rand::rng(); // summoned the messenger (mut, because it changes with every roll)
    // rand 0.9 and newer: rand::rng()
    // rand 0.8 and older: rand::thread_rng()

    let dice = rng.random_range(1..=6);         // from 1 to 6 INCLUDING the six
    let percent = rng.random_range(0..100);     // from 0 to 99 (100 is NOT included!)
    let chance = rng.random_bool(0.3);          // true with a 30% chance
    // rand 0.9 and newer: random_range() and random_bool()
    // rand 0.8 and older: gen_range() and gen_bool()

    println!("Dice: {dice}, percent: {percent}, chance: {chance}");
}
```

###### 🔍 Breaking it down

- **`use rand::RngExt;`**: this is the “license” that gives the messenger the right to roll dice. `RngExt` is a trait (a set of abilities). If you don’t import it, Rust will say the method `random_range` doesn’t exist. This is the most common beginner mistake!
- **`let mut rng`**: `mut` is needed because the generator’s internal state changes after every roll.
- **`1..=6`** and **`0..100`**: these are ranges. With the `=` sign the right end **is included**, without it, it **is not**.

##### 🎴 4. Working with lists: pick and shuffle

For vectors you need two more “licenses” from the `seq` module:

```Rust
use rand::seq::{IndexedRandom, SliceRandom};
// IndexedRandom (the choose method) appeared in rand 0.9.
// In rand 0.8 and older, choose lived in SliceRandom, and IndexedRandom didn't exist.
use rand::RngExt; // rand 0.10+ (in 0.9 and older: use rand::Rng;)

fn main() {
    let mut rng = rand::rng();
    let mut slaves = vec!["Spartacus", "Gaius", "Marcus", "Lucius"];

    // Pick ONE at random (returns an Option, in case the list is empty!)
    if let Some(lucky) = slaves.choose(&mut rng) {
        println!("Freedom goes to: {lucky}");
    }

    // Shuffle the whole list (changes the vector itself, so it must be mut)
    slaves.shuffle(&mut rng);
    println!("New order: {slaves:?}");
}
```

###### 🔍 Breaking it down

- **`choose`** returns an `Option`, because there’s nothing to pick from an empty list. Rust makes you think about that.
- **`&mut rng`**: we **lend the messenger out** (remember borrowing?). The method takes a mutable reference to our generator, because the generator changes when it rolls.
- **`shuffle`** changes the list itself instead of creating a new one. That’s why `slaves` is declared `mut`.

##### 🌱 5. The seed: the same fate every time

A regular generator gives different numbers every time. But sometimes you want randomness to be **repeatable**: for tests, or for a game where the world is built from a “seed” (like in Minecraft). In that case you give the generator a **seed**:

```Rust
use rand::{RngExt, SeedableRng};
// RngExt: rand 0.10+ (in 0.9 and older: Rng)
use rand::rngs::StdRng;

fn main() {
    let mut rng = StdRng::seed_from_u64(42); // seed 42

    let x = rng.random_range(1..=100);
    println!("{x}"); // the SAME number on EVERY run!
}
```

The same seed gives the same sequence of “random” numbers. This is called **pseudo-randomness**: the numbers look random, but they are actually computed by a formula from the seed.

##### 🩺 6. How not to suffer with versions: read the compiler

When your code doesn’t compile because of a version change, **don’t google right away**. The Rust compiler usually tells you what to do. For example, on the `random_range` error it gave this hint:

```bash
help: trait `RngExt` which provides `random_range` is implemented but not in scope;
      perhaps you want to import it
  1 + use rand::RngExt;
```

This means: “the method exists, but the trait isn’t imported, here’s the line you need to add”. Look for the word **`help:`** in the output. The compiler’s hints are specific to the exact version you have installed, so they’re more accurate than any tutorial.

##### 🧠 Quick cheat sheet

1. `cargo add rand`: add the crate.
2. `rand::random::<type>()`: one quick roll, the type is required.
3. `let mut rng = rand::rng();` + `use rand::RngExt;`: your own generator, without the `use` the methods won’t be found.
4. `rng.random_range(1..=6)`: a number in a range (`..=` includes the end, `..` doesn’t).
5. `rng.random_bool(0.3)`: `true` with the given probability.
6. `.choose(&mut rng)` and `.shuffle(&mut rng)`: pick from a list and shuffle it (needs `use rand::seq::{IndexedRandom, SliceRandom};`).
7. `StdRng::seed_from_u64(42)`: repeatable randomness from a seed.
8. **Version matters!**
    - 0.8 and older: `thread_rng()`, `gen_range()`, `gen_bool()`, trait `Rng`
    - 0.9: `rng()`, `random_range()`, `random_bool()`, trait `Rng`
    - 0.10: `rng()`, `random_range()`, `random_bool()`, trait **`RngExt`**
9. Something stopped compiling after an update? Look for **`help:`** in the error, there’s usually a ready-made fix.