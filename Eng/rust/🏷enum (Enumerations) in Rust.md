## 1. What is `enum`?

**`enum` (enumeration / enumeration)** is a data type that allows you to select **one option from a strictly limited list**.

- **Logic `struct`:** Both this, And this (contains a set of fields at the same time).

- **Logic `enum`:** EITHER OR THE OTHER (the variable can contain **only one** of the options at a given time).


## 2. Rust trick: Storing data inside options

In Rust, `enum` variants are not just shortcuts, they can **directly store data**, and different variants can store completely different types!



```Rust
enum PaymentMethod {
Card(u64, u16), // Option 1: stores the card number (u64) and CVC-
Crypto(String), // Option 2: stores wallet address (String)
Cash, // Option 3: no data at all (cash)
}
```

## 3. How it works in code (Full example with `match`)

The main benefit of `enum` is revealed with the **`match`** construct, which allows you to safely “unpack” data from an option.



```Rust
enum PaymentMethod {
    Card(u64, u16),
    Crypto(String),
    Cash,
}

fn process_order(payment: PaymentMethod) {
    // match looks at WHICH option came and pulls out the data
    match payment {
        PaymentMethod::Card(number, cvc) => {
            println!(
                "We are debiting money from the card {} (CVC: {})",
                number, cvc
            );
        }
        PaymentMethod::Crypto(wallet) => {
            println!("Waiting for a transaction to the wallet: {}", wallet);
        }
        PaymentMethod::Cash => {
            println!("Payment in cash upon receipt.");
        }
    }
}

fn main() {
    // Create variables with different contents
    let client_a_pay = PaymentMethod::Card(4400123456789012, 777);
    let client_b_pay = PaymentMethod::Crypto(String::from("0x71C...89"));

    // Pass different options to the SAME function!
    process_order(client_a_pay);
    process_order(client_b_pay);
}

```

## 4. Main advantages and why it is needed

1. **One function instead of a bunch of crutches:** No need to write separate functions for each life situation (`process_card`, `process_crypto`). You pass one `enum` to one function.

2. **Compiler safety:** The `match` construct forces **all** possible options from the `enum` to be processed. If you forget to add the `Cash` branch, the code simply will not compile.

3. **Strong typing:** You cannot accidentally pass a non-existent variant or mix up the data format.
