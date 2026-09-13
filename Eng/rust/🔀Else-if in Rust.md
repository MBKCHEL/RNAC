In novels, we constantly need to check what items the player has and what their condition is.

### Logical operators:

- `&&` - **And** (both factors need to coincide).

- `||` - **OR** (it is enough for at least one factor to match).


## 1. Regular `if / else if / else`

Used when you need to check specific conditions one by one. Parentheses around the condition are not necessary, curly brackets are required.

### Example for a novella:



```Rust
let choice = 2; // Select player in console

if choice == 1 {
    println!("You have opened the left door.");
} else if choice == 2 {
    println!("You have opened the right door.");
} else {
    println!("You are standing still.");
}
```

### Example: Selecting an answer in a dialogue with checking two conditions

Imagine the situation: a player is standing in front of a locked door in a library, and his partner is talking to him.



```Rust
let choice = 2; // Number of the response that the player entered into the console
let has_key = false; // Did the player find the key earlier in the story?
let charisma = 7; // Level of eloquence upgrade

if choice == 1 && has_key == true {
    // Condition 1: The player chose option 1 And he really has the key
    println!("You insert a rusty key into the keyhole. The door creaks open.");
} else if choice == 2 || charisma >= 5 {
    // Condition 2: The player chose option 2 OR he simply has high charisma
    println!(
        "You charm your partner's teeth, and he helps you knock down the door with his shoulder."
    );
} else {
    // All other cases (wrong choice or not enough stats)
    println!("Your partner looks at you like you're a fool. Nothing happens.");
}

```
