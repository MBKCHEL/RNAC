## 🎯 Why you need it

The **`dirs`** crate provides correct paths to standard user directories (Home, Config, Music, Documents) regardless of where the code is executed - Linux, Windows, or macOS.

* **Main advantage:** Saves you from hardcoding paths like `/home/user/` or `C:\Users\...`.

## 🛠 Dependency Setup (`Cargo.toml`)

```toml
[dependencies]
dirs = "5.0"
```

## ⚡ Top Functions (Most Important)

All functions return `Option<PathBuf>`, since in theory a directory might not exist on the system.

| **Function** | **Linux / macOS** | **Windows** |
| :--- | :--- | :--- |
| **`dirs::home_dir()`** | `/home/username` | `C:\Users\Username` |
| **`dirs::audio_dir()`** | `/home/username/Music` | `C:\Users\Username\Music` |
| **`dirs::config_dir()`** | `/home/username/.config` | `C:\Users\Username\AppData\Roaming` |
| **`dirs::download_dir()`** | `/home/username/Downloads` | `C:\Users\Username\Downloads` |

## 💡 Basic Template

```rust
use dirs;
use std::fs;

fn main() {
    // 1. Get user home directory
    if let Some(mut path) = dirs::home_dir() {
        // 2. Append directory name to the path
        path.push("SysPMF");

        // 3. Create directory if it does not exist
        if !path.exists() {
            fs::create_dir_all(&path).unwrap();
            println!("Created directory: {:?}", path);
        }
    }
}
```