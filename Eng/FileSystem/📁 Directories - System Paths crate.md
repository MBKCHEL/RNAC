**Description:** A cross-platform crate for obtaining standard system paths (Home, Config, Cache, Data, etc.) according to OS specifications (Linux XDG, macOS, Windows).

**Cargo.toml:** 

``` TOML
[dependencies]
directories = "5.0" # or current version
```

## 1. Main Structures

The crate provides three main structures:

- **`BaseDirs`** - basic user directories (`home_dir`, `config_dir`, `cache_dir`, `data_dir`).
    
- **`UserDirs`** - standard user folders (`audio_dir`, `document_dir`, `download_dir`, `picture_dir`, etc.).
    
- **`ProjectDirs`** - paths tied to a specific application/project (created using the qualifier scheme: qualifier, organization, application name).
    

## 2. Usage Examples

### A. Obtaining Basic User Paths (`BaseDirs`)



```Rust
use directories::BaseDirs;

fn main() {
    if let Some(base_dirs) = BaseDirs::new() {
        println!("Home:   {:?}", base_dirs.home_dir());
        println!("Config: {:?}", base_dirs.config_dir()); // e.g. ~/.config
        println!("Cache:  {:?}", base_dirs.cache_dir());  // e.g. ~/.cache
        println!("Data:   {:?}", base_dirs.data_dir());   // e.g. ~/.local/share
    }
}
```

### B. Paths for User Files (`UserDirs`)



```Rust
use directories::UserDirs;

fn main() {
    if let Some(user_dirs) = UserDirs::new() {
        if let Some(downloads) = user_dirs.download_dir() {
            println!("Download dir: {:?}", downloads);
        }
        if let Some(desktop) = user_dirs.desktop_dir() {
            println!("Desktop dir: {:?}", desktop);
        }
    }
}
```

### C. Project Config and Data Paths (`ProjectDirs`)

_The most common pattern for CLI tools and apps._



```Rust
use directories::ProjectDirs;

fn main() {
    // ProjectDirs::from(qualifier, organization, application)
    if let Some(proj_dirs) = ProjectDirs::from("com", "mbkchel", "rnac") {
        let config_dir = proj_dirs.config_dir();
        // Linux:   ~/.config/rnac
        // Windows: C:\Users\Username\AppData\Roaming\mbkchel\rnac
        // macOS:   /Users/Username/Library/Application Support/com.mbkchel.rnac

        println!("Project Config Dir: {:?}", config_dir);
        println!("Project Cache Dir:  {:?}", proj_dirs.cache_dir());

        // Example of getting a config file path:
        let config_file = config_dir.join("config.toml");
        println!("Config file path:   {:?}", config_file);
    }
}
```

## 3. Practice: Folder Retrieval and Creation

In real-world applications (like CLI utilities), folders are created via `fs::create_dir_all`, and errors are softly logged using `if let Err(e)` - preventing application crashes if user permissions are missing.



```Rust
use directories::UserDirs;
use std::fs;

fn main() {
    // 1. Get base user directories
    if let Some(user_dirs) = UserDirs::new() {
        // 2. Build the target path (e.g., ~/SysPMF)
        let path = user_dirs.home_dir().join("SysPMF");

        // 3. Create the folder if it does not exist yet.
        // Log an error message if an issue occurs (e.g., missing permissions)
        if let Err(e) = fs::create_dir_all(&path) {
            eprintln!("Error creating directory: {e}");
        }

        // 4. Proceed with the guaranteed existing path
        println!("Working directory: {:?}", path);
    }
}
```

### Key Takeaways for Notes:

- **`fs::create_dir_all(&path)`** - `mkdir -p` equivalent in Linux/Bash. Creates the entire path recursively.
    
- **`path.exists()`** - fast check to verify if a file or directory already exists.
    
- **`dir.join("filename")`** - safe path joining via `PathBuf`, automatically accounting for OS-specific slashes (`/` or `\`).
    

## 4. Use Cases

- **Storing CLI tool configs** (`ProjectDirs::config_dir()`).
    
- **Caching data** (`ProjectDirs::cache_dir()`).
    
- **Locating user Downloads/Documents** (`UserDirs`).
