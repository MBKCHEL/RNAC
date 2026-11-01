**Description:** A fast, cross-platform crate for recursive filesystem traversal. Automatically handles nested folders, symlinks, and iterator mechanics.

**Cargo.toml:**

```TOML
[dependencies]
walkdir = "2.5" # or current version
```

## 1. Core Concepts

The main type is `WalkDir::new("path")`. It returns an iterator over `Result<DirEntry, walkdir::Error>`.

Key methods on `DirEntry`:

- **`.path()`** - returns `&Path` to the current item.
    
- **`.file_name()`** - file or folder name as `&OsStr`.
    
- **`.file_type()`** - checks the item type (`is_file()`, `is_dir()`, `is_symlink()`).
    
- **`.depth()`** - current nesting level relative to root (0 = the root directory itself).
    
- **`.metadata()`** - retrieves file size and permissions on the fly.
    

## 2. Usage Examples

### A. Basic Recursive Traversal with Safe Error Handling

_Ignores permission errors (e.g., protected system folders) without crashing the application._

```Rust
use walkdir::WalkDir;

fn main() {
    for entry in WalkDir::new("/home/mbkchel")
        .into_iter()
        .filter_map(|e| e.ok()) // Softly skip erroneous paths
    {
        println!("{}", entry.path().display());
    }
}
```

### B. Scanning Audio Tracks for Playlists (SysPMF Style)

_Filters out directories and keeps only specific file extensions (`.mp3`, `.flac`, `.wav`)._

```Rust
use walkdir::WalkDir;

fn main() {
    let music_dir = "/home/mbkchel/Music";

    let audio_files: Vec<_> = WalkDir::new(music_dir)
        .into_iter()
        .filter_map(|e| e.ok())
        .filter(|e| e.file_type().is_file()) // Exclude directories
        .filter(|e| {
            e.path()
                .extension()
                .and_then(|ext| ext.to_str())
                .map(|ext| matches!(ext, "mp3" | "flac" | "wav"))
                .unwrap_or(false)
        })
        .collect();

    for file in audio_files {
        println!("Found track: {:?}", file.path());
    }
}
```

### C. Skipping Heavy/Hidden Folders (`filter_entry`)

_The `filter_entry` method skips subdirectories before entering them, saving I/O resources._

```Rust
use walkdir::WalkDir;

fn is_ignored(entry: &walkdir::DirEntry) -> bool {
    let name = entry.file_name().to_str().unwrap_or("");

    // 1. Pass root directory pointers
    if name == "." || name == ".." {
        return false;
    }

    // 2. Ignore hidden files/folders and target directories
    name.starts_with('.') || name == "target" || name == "node_modules"
}

fn main() {
    for entry in WalkDir::new(".")
        .into_iter()
        .filter_entry(|e| !is_ignored(e)) // Prunes the entire branch
        .filter_map(|e| e.ok())
    {
        if entry.file_type().is_file() {
            println!("File: {:?}", entry.path());
        }
    }
}
```

### D. Alphabetical Sorting Before Iteration

```Rust
use walkdir::WalkDir;

fn main() {
    for entry in WalkDir::new("/home/mbkchel/Music")
        .sort_by_file_name() // Sorts at each level of the tree
        .into_iter()
        .filter_map(|e| e.ok())
        .filter(|e| e.file_type().is_file())
    {
        println!("Item: {:?}", entry.file_name());
    }
}
```

### E. Calculating Directory Size

```Rust
use walkdir::WalkDir;

fn get_dir_size(path: &str) -> u64 {
    WalkDir::new(path)
        .into_iter()
        .filter_map(|e| e.ok())
        .filter(|e| e.file_type().is_file())
        .filter_map(|e| e.metadata().ok())
        .map(|meta| meta.len()) // Size in bytes
        .sum()
}

fn main() {
    let size_bytes = get_dir_size("./src");
    println!("Directory size: {} bytes", size_bytes);
}
```

## 3. Key Takeaways for Notes

- **`filter_map(|e| e.ok())`** - idiomatic filter for unwrapping `Result<DirEntry, Error>` without panicking (`panic!`).
    
- **`filter_entry(|e| ...)`** - prunes entire directory trees early, preventing redundant disk reads.
    
- **`.max_depth(n)`** - caps recursion depth (useful for fast scans).
    
- **`.sort_by_file_name()`** - sorts entries alphabetically on the fly.
    

## 4. Use Cases

- **Media indexing** (library scanning in CLI players).
    
- **System analyzers** (directory size calculations, log searching).
    
- **Source code parsers** (collecting all files with `.rs` extension).
