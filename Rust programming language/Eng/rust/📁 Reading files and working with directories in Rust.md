# 📁 Reading files and working with directories in Rust

All basic work with the file system is in the `std::fs` module.

---

## 1. Read one file

### Text file (`fs::read_to_string`)
Returns `Result<String, io::Error>`. Works with UTF-8 text.

```rust
use std::fs;

fn main() {
// Read the entire file into one String variable
let content = fs::read_to_string("example.txt").expect("Error reading file");
println!("File content:\n{}", content);
}
```

### Binary file (`fs::read`)

Returns `Result<Vec<u8>, io::Error>`. Reads raw bytes (pictures, executable files, archive, etc.).



```Rust
use std::fs;

fn main() {
// Reading into a byte array, /home/mbkchel/image.png is the path, to home in Linux (mbkchel) my user
let bytes = fs::read("/home/mbkchel/image.png").expect("Error");

println!("Bytes read: {}", bytes.len());
}
```

### **A very good example demonstrates error handling and displaying the contents of a text file.**
```rust
use std::fs::File;
use std::io::Read;

fn read_username_from_file() -> Result<String, std::io::Error> {
    let f = File::open("/home/mbkchel/hello.txt");

    let mut f = match f {
        Ok(file) => file,
        Err(e) => return Err(e),
    };

    let mut s = String::new();

    let _ = match f.read_to_string(&mut s) {
        Ok(_) => Ok(&s),
        Err(e) => Err(e),
    };

    Ok(s)
}

fn main() {
    match read_username_from_file() {
        Ok(username) => println!("allright read: {}", username),
        Err(error) => println!("Error: {}", error),
    }
}
```
## 2. Read ALL files in the folder (`fs::read_dir`)

2. Reading ALL files in a folder (`fs::read_dir`)
    
    The `fs::read_dir` function returns an iterator over all items inside a folder (files, subfolders, symlinks).
    

Since the folder might be inaccessible (no permissions, deleted during execution) or some file inside is corrupted, each element in this iterator is a `Result<DirEntry, io::Error>`.

```Rust
use std::env;
use std::fs;
use std::path::PathBuf;

/// Cross-platform retrieval of the ~/SysPMF path (or C:\Users\<User>\SysPMF)
fn get_app_dir() -> PathBuf {
    let home = if cfg!(unix) {
        env::var("HOME").unwrap_or_else(|_| "/tmp".into())
    } else if cfg!(windows) {
        env::var("USERPROFILE").unwrap_or_else(|_| r"C:\".into())
    } else {
        ".".to_string()
    };

    PathBuf::from(home).join("SysPMF") // you can write PathBuf::from(home).join("SysPMF").join("Imba Playlist") to open that specific directory
}

fn main() {
    let dir_path = get_app_dir();

    // 1. Automatic cross-generation of the folder if it doesn't exist yet
    if !dir_path.exists() {
        println!("Folder {:?} not found, creating...", dir_path);
        fs::create_dir_all(&dir_path).expect("Failed to create directory");
    }

    // 2. Reading files from the folder
    let entries = fs::read_dir(&dir_path).expect("Failed to open directory");

    println!("--- Contents of folder {:?} ---", dir_path);

    // Use .flatten() to filter out individual file reading errors
    for entry in entries.flatten() {
        let path = entry.path();
        println!("Found object: {:?}", path);
    }
}
```

### 3. Creating a folder if it doesn’t exist, reading all the elements in it, cross-platform



```Rust
use std::env;
use std::fs;
use std::path::{Path, PathBuf};

fn get_user_dir() -> PathBuf {
    // Unix-like systems: Linux, macOS, FreeBSD, NetBSD, OpenBSD
    let home = if cfg!(unix) {
        env::var("HOME").unwrap_or_else(|_| "/tmp".into()) // Unix like system, they have a home
    } else if cfg!(windows) {
        //windows
        env::var("USERPROFILE").unwrap_or_else(|_| r"C:\".into())
    } else {
        ".".to_string()
    };
    PathBuf::from(home).join("my_folder")
}

fn main() {
    let dir_path: PathBuf = get_user_dir(); // Get the user axis
    let path: &Path = Path::new(&dir_path); // Make a directory

    if !path.exists() {
        println!("There is no folder, we are creating...");
        // create_dir_all will create the entire chain of folders if there are none
        fs::create_dir_all(path).expect("Could not create folder");
    }

    // Open the directory
    let entries = fs::read_dir(&dir_path).expect("Could not open directory");

    for entry in entries {
        // Each element is Result<DirEntry, io::Error>
        let entry = entry.expect("Error reading element");
        let path = entry.path();

        println!("Object found: {:?}", path);
    }
}

```

### 4. Full example: Filtering (FILES ONLY) and reading their contents

The example scans a folder, selects only files (skipping subfolders) and displays their names:


```rust
use std::env;
use std::fs;
use std::path::{Path, PathBuf};

/// Returns the path to the working folder in the home directory
fn get_user_dir() -> PathBuf {
    let home = if cfg!(unix) {
        env::var("HOME").unwrap_or_else(|_| "/tmp".into())
    } else if cfg!(windows) {
        env::var("USERPROFILE").unwrap_or_else(|_| r"C:\".into())
    } else {
        ".".to_string()
    };
    PathBuf::from(home).join("my_folder")
}

/// Securely scans a directory and displays the contents of text files.
/// Accepts `&Path`, allowing PathBuf, &PathBuf, or &Path to be passed here.
fn scan_and_read_all(dir_path: &Path) {
    let entries = match fs::read_dir(dir_path) {
        Ok(entries) => entries,
        Err(err) => {
            eprintln!("Error accessing directory {:?}: {}", dir_path, err);
            return;
        }
    };

    // entries.flatten() automatically skips elements
    // while reading which an IoError occurred
    for entry in entries.flatten() {
        let path = entry.path();

        // 1. Check that this is a file and not a subdirectory
        if path.is_file() {
            println!("------------------------------------------------");
            if let Some(name) = path.file_name() {
                println!("File: {:?}", name);
            }
            println!("Full path: {:?}", path);

            // 2. Read the content (safely catch non-UTF8 and binaries)
            if let Ok(content) = fs::read_to_string(&path) {
                println!("Content:\n{}", content);
            } else {
                println!("(Binary file or non-UTF-8)");
            }
        }
    }
}

fn main() {
    let target_dir = get_user_dir();

    // Ensure the folder exists before reading
    if !target_dir.exists() {
        let _ = fs::create_dir_all(&target_dir);
    }

    // Pass a link to PathBuf (&target_dir is automatically converted to &Path)
    scan_and_read_all(&target_dir);
}

```

### Something like this, filter mp3/vav/ogg

```rust
use std::env;
use std::fs;
use std::path::{Path, PathBuf};

/// Returns the cross-platform path to the user's working folder
fn get_user_dir() -> PathBuf {
    let home = if cfg!(unix) {
        env::var("HOME").unwrap_or_else(|_| "/tmp".into())
    } else if cfg!(windows) {
        env::var("USERPROFILE").unwrap_or_else(|_| r"C:\".into())
    } else {
        ".".to_string()
    };
    PathBuf::from(home).join("my_folder")
}

/// Scans a directory and outputs only audio files (mp3, wav, ogg)
fn scan_audio_files(dir_path: &Path) {
    let entries = match fs::read_dir(dir_path) {
        Ok(entries) => entries,
        Err(err) => {
            eprintln!("Error accessing directory {:?}: {}", dir_path, err);
            return;
        }
    };

    println!("--- Scan audio files in {:?} ---", dir_path);

    for entry in entries.flatten() {
        let path = entry.path();

        if path.is_file() {
            // Check the file extension to see if it matches mp3, wav or ogg
            let is_audio = path
                .extension()
                .and_then(|ext| ext.to_str())
                .map(|ext| ext.to_lowercase())
                .map_or(false, |ext| matches!(ext.as_str(), "mp3" | "wav" | "ogg"));

            if is_audio {
                println!("------------------------------------------------");
                if let Some(name) = path.file_name() {
                    println!("Audio file: {:?}", name);
                }
                println!("Full path: {:?}", path);
            }
        }
    }
}

fn main() {
    let target_dir = get_user_dir();

    // 1. Check the existence of the folder, if not, create the entire chain
    if !target_dir.exists() {
        println!("Folder {:?} not found, creating...", target_dir);
        fs::create_dir_all(&target_dir).expect("Could not create target folder");
    } else {
        println!("Working folder found: {:?}", target_dir);
    }

    // 2. Launch the scanner only for the required audio formats
    scan_audio_files(&target_dir);
}

```

## 5. Checking if the path exists and creating a folder

Before scanning or saving files, you will often want to check if the path exists:



```Rust
use std::env;
use std::fs;
use std::path::{Path, PathBuf};

fn get_user_dir() -> PathBuf {
    // Unix-like systems: Linux, macOS, FreeBSD, NetBSD, OpenBSD
    let home = if cfg!(unix) {
        env::var("HOME").unwrap_or_else(|_| "/tmp".into()) // Unix like system, they have a home
    } else if cfg!(windows) {
        //windows
        env::var("USERPROFILE").unwrap_or_else(|_| r"C:\".into())
    } else {
        ".".to_string()
    };
    PathBuf::from(home).join("my_folder")
}

fn main() {
    let dir_path: PathBuf = get_user_dir(); // Get the user axis
    let path: &Path = Path::new(&dir_path); // Make a directory

    if !path.exists() {
        println!("There is no folder, we are creating...");
        // create_dir_all will create the entire chain of folders if there are none
        fs::create_dir_all(path).expect("Could not create folder");
    }
}
```

## 🎯 Cheat sheet by types and functions

- `fs::read_to_string(path)` - reads the file in `String`.

- `fs::read(path)` - reads the file in `Vec<u8>`.

- `fs::read_dir(path)` - returns an iterator of folder elements.

- `path.is_file()` - checks whether the element is a file.

- `path.is_dir()` - checks whether the element is a directory.

- `path.exists()` - checks the presence of a file/folder along the path.
