`match` is ideal for handling player choices (`1`, `2`, `3`) when there are many story branches.

- `|` - means **OR** (multiple choices lead to the same result).

- `_` - means **“any other input”** (protection from a moron if the player entered a random number or letter into the console).


### A tasty introductory example:

```rust
#[derive(Debug)]
enum UsState {
    Alaska,
    Alabama,
    California,
}

enum Coin {
    Penny,
    Quarter(UsState), // Quarter, inside of which lies the state
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        // Unpack the state from the quarter directly into the state variable:
        Coin::Quarter(state) => {
            println!("Wow, got a quarter from the state of {:?}!", state);
            25
        }
    }
}

fn main() {
    // 1. Create a coin with the state of Alaska
    let my_coin = Coin::Quarter(UsState::Alaska);

    // 2. Pass it to the function
    let cents = value_in_cents(my_coin);

    println!("Coin value: {} cents", cents);
}

```
### Example 1, we ask the user what month it is and he displays what time of year:

``` rust
use std::io;

fn main() {
    let seasons = ["Winter", "Spring", "Summer", "Autumn"];

    let mut user_month = String::new();

    io::stdin()
        .read_line(&mut user_month)
        .expect("Failed to read line");

    let user_month = user_month.trim();

    match user_month.to_lowercase().as_str() {
        "january" | "december" | "february" => println!("{}", seasons[0]),
        "march" | "april" | "may" => println!("{}", seasons[1]),
        "june" | "july" | "august" => println!("{}", seasons[2]),
        "september" | "october" | "november" => println!("{}", seasons[3]),
        _ => println!("Unknown month"),
    }
}

```

## I use match user_month.to_lowercase().as_str() so that when the user enters the month with a small or capital letter, or in caps, it doesn’t screw him up

### Example 2: Handling a selection in a dialog menu (with the `|` operator)



```Rust
let dialog_choice = 3;

match dialog_choice {
    1 => println!("You say: \"Give up!\" The bandit just laughs in response."),
    2 | 3 => {
        // Will work if the player entered 2 OR 3
        println!("You decide to remain silent or step back. The atmosphere is heating up.");
    }
    _ => println!("You hesitated and did not have time to do anything. Time was lost."),
}

```

### Example 3: Checking 2 conditions in `match` (Choice + Character character)

To check two conditions at once (for example, what phrase the player chose + what character he currently has: “good” or “evil”), we combine them in parentheses `(choice, character)`.



```Rust
let choice = 1;
let alignment = "angry";

match (choice, alignment) {
    (1, "kind") => {
        println!("You politely ask the guard to let you through. He smiles.");
    }
    (1, "angry") => {
        println!("You order through clenched teeth to move away. The guard grabs his sword.");
    }
    (2, _) => {
        println!("You are just passing by, ignoring the guards.");
    }
    // The `_` stub above means that when choosing 2, the character's character is not important at all
    _ => {
        println!("You entered something wrong, the guard squinted suspiciously.");
    }
}

}
```

### Example 4: Checking conditions on the fly inside `match` (Match Guards)

Sometimes inside `match` you need to check a numeric parameter (for example, how much money the player has to buy information in a tavern). To do this, write `if` right after the option.



```Rust
let choice = 3; // The player chooses to buy a secret card for 50 coins
let gold = 45;

match choice {
3 if gold >= 50 => println!("You give 50 coins and take the card."),
3 if gold < 50 => println!("The innkeeper hides the card: “Come back when you get rich!”"),
_ => println!("You just drink kvass and listen to rumors."),
}
```
## 5. Regular `match`

Used as a replacement for long `if / else`. It compares the value of a variable with a list of options.

- `=>` - separates the option from the action.

- `_` is a mandatory final option for all other cases that do not fit the upper numbers.


### Example for a novella:



```Rust
let item = "key"; // What the player took from inventory

match item {
"coin" => println!("You threw a coin into the fountain."),
"key" => println!("You have opened the lock."),
_ => println!("This item is useless here."),
}
```
