# 📁 Чтение файлов и работа с директориями в Rust

Вся базовая работа с файловой системой находится в модуле `std::fs`.

---

## 1. Чтение одного файла

### Текстовый файл (`fs::read_to_string`)
Возвращает `Result<String, io::Error>`. Работает с UTF-8 текстом.

```rust
use std::fs;

fn main() {
    // Чтение всего файла в одну String-переменную
    let content = fs::read_to_string("example.txt").expect("Ошибка чтения файла");
    println!("Содержимое файла:\n{}", content);
}
```

### Бинарный файл (`fs::read`)

Возвращает `Result<Vec<u8>, io::Error>`. Читает сырые байты (картинки, исполняемые файлы, архив и т.д.).



```Rust
use std::fs;

fn main() {
    // Чтение в массив байт, /home/mbkchel/image.png это путь, в хоум в линукс (mbkchel) мой юзер
    let bytes =  fs::read("/home/mbkchel/image.png").expect("Ошибка");
    
    println!("Прочитано байт: {}", bytes.len());
}
```
## 2. Чтение ВСЕХ файлов в папочке (`fs::read_dir`)

Функция `fs::read_dir` возвращает итератор по всем объектам внутри папки (файлы, подпапки, симлинки).

### 3. Создание папки если её нету, чтение всех элементов в ней, кроссплатформенность



```Rust
use std::env;
use std::fs;
use std::path::{Path, PathBuf};

fn get_user_dir() -> PathBuf {
    // Unix-подобные системы: Linux, macOS, FreeBSD, NetBSD, OpenBSD
    let home = if cfg!(unix) {
        env::var("HOME").unwrap_or_else(|_| "/tmp".into()) // Unix like system, у них хоум
    } else if cfg!(windows) {
        // windows
        env::var("USERPROFILE").unwrap_or_else(|_| r"C:\".into())
    } else {
        ".".to_string()
    };
    PathBuf::from(home).join("my_folder")
}

fn main() {
    let dir_path: PathBuf = get_user_dir(); // Получаем ось юзера
    let path: &Path = Path::new(&dir_path); // Делаем директорию

    if !path.exists() {
        println!("Папки нет, создаём...");
        // create_dir_all создаст всю цепочку папок, если их нет
        fs::create_dir_all(path).expect("Не удалось создать папку");
    }

    // Открываем директорию
    let entries = fs::read_dir(&dir_path).expect("Не удалось открыть директорию");

    for entry in entries {
        // Каждый элемент - это Result<DirEntry, io::Error>
        let entry = entry.expect("Ошибка чтения элемента");
        let path = entry.path();

        println!("Найден объект: {:?}", path);
    }
}
```

### 4. Полный пример: Фильтрация (Только ФАЙЛЫ) и чтение их содержимого

Пример сканирует папку, отбирает только файлы (пропуская вложенные папки) и выводит их имена:


```rust
use std::env;
use std::fs;
use std::path::{Path, PathBuf};

/// Возвращает путь к рабочей папке в домашней директории
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

/// Безопасно сканирует директорию и выводит содержимое текстовых файлов.
/// Принимает `&Path`, что позволяет передавать сюда PathBuf, &PathBuf или &Path.
fn scan_and_read_all(dir_path: &Path) {
    let entries = match fs::read_dir(dir_path) {
        Ok(entries) => entries,
        Err(err) => {
            eprintln!("Ошибка доступа к директории {:?}: {}", dir_path, err);
            return;
        }
    };

    // entries.flatten() автоматически пропускает элементы,
    // при чтении которых возникла ошибка IoError
    for entry in entries.flatten() {
        let path = entry.path();

        // 1. Проверяем, что это файл, а не поддиректория
        if path.is_file() {
            println!("----------------------------------");
            if let Some(name) = path.file_name() {
                println!("Файл: {:?}", name);
            }
            println!("Полный путь: {:?}", path);

            // 2. Читаем содержимое (безопасно отлавливаем не-UTF8 и бинарники)
            if let Ok(content) = fs::read_to_string(&path) {
                println!("Содержимое:\n{}", content);
            } else {
                println!("(Бинарный файл или не UTF-8)");
            }
        }
    }
}

fn main() {
    let target_dir = get_user_dir();

    // Гарантируем наличие папки перед чтением
    if !target_dir.exists() {
        let _ = fs::create_dir_all(&target_dir);
    }

    // Передаем ссылку на PathBuf (&target_dir автоматически приводится к &Path)
    scan_and_read_all(&target_dir);
}
```

### Что-то на подобии, фильтруем мп3/вав/огг

```rust
use std::env;
use std::fs;
use std::path::{Path, PathBuf};

/// Возвращает кроссплатформенный путь к рабочей папке пользователя
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

/// Сканирует директорию и выводит только аудиофайлы (mp3, wav, ogg)
fn scan_audio_files(dir_path: &Path) {
    let entries = match fs::read_dir(dir_path) {
        Ok(entries) => entries,
        Err(err) => {
            eprintln!("Ошибка доступа к директории {:?}: {}", dir_path, err);
            return;
        }
    };

    println!("--- Сканирование аудиофайлов в {:?} ---", dir_path);

    for entry in entries.flatten() {
        let path = entry.path();

        if path.is_file() {
            // Проверяем расширение файла на совпадение с mp3, wav или ogg
            let is_audio = path
                .extension()
                .and_then(|ext| ext.to_str())
                .map(|ext| ext.to_lowercase())
                .map_or(false, |ext| matches!(ext.as_str(), "mp3" | "wav" | "ogg"));

            if is_audio {
                println!("----------------------------------");
                if let Some(name) = path.file_name() {
                    println!("Аудиофайл: {:?}", name);
                }
                println!("Полный путь: {:?}", path);
            }
        }
    }
}

fn main() {
    let target_dir = get_user_dir();

    // 1. Проверяем существование папки, если нет - создаём вся цепочку
    if !target_dir.exists() {
        println!("Папка {:?} не найдена, создаём...", target_dir);
        fs::create_dir_all(&target_dir).expect("Не удалось создать целевую папку");
    } else {
        println!("Рабочая папка найдена: {:?}", target_dir);
    }

    // 2. Запускаем сканер только по нужным аудио-форматам
    scan_audio_files(&target_dir);
}
```

## 5. Проверка существования пути и создание папки

Перед сканированием или сохранением файлов часто нужно проверить, существует ли путь:



```Rust
use std::env;
use std::fs;
use std::path::{Path, PathBuf};

fn get_user_dir() -> PathBuf {
    // Unix-подобные системы: Linux, macOS, FreeBSD, NetBSD, OpenBSD
    let home = if cfg!(unix) {
        env::var("HOME").unwrap_or_else(|_| "/tmp".into()) // Unix like system, у них хоум
    } else if cfg!(windows) {
        // windows
        env::var("USERPROFILE").unwrap_or_else(|_| r"C:\".into())
    } else {
        ".".to_string()
    };
    PathBuf::from(home).join("my_folder")
}

fn main() {
    let dir_path: PathBuf = get_user_dir(); // Получаем ось юзера
    let path: &Path = Path::new(&dir_path); // Делаем директорию

    if !path.exists() {
        println!("Папки нет, создаём...");
        // create_dir_all создаст всю цепочку папок, если их нет
        fs::create_dir_all(path).expect("Не удалось создать папку");
    }

```

## 🎯 Шпаргалка по типам и функциям

- `fs::read_to_string(path)` - читает файл в `String`.
    
- `fs::read(path)` - читает файл в `Vec<u8>`.
    
- `fs::read_dir(path)` - возвращает итератор элементов папки.
    
- `path.is_file()` - проверка, является ли элемент файлом.
    
- `path.is_dir()` - проверка, является ли элемент директорией.
    
- `path.exists()` - проверка наличия файла/папки по пути.
