**Описание:** Кроссплатформенный клейт для быстрых рекурсивных обходов файловой системы. Автоматически обрабатывает вложенные папки, символьные ссылки и интеграцию с итераторами.

**Cargo.toml:**

```TOML
[dependencies]
walkdir = "2.5" # или актуальная версия
```

## 1. Основные концепты

Главный тип - `WalkDir::new("path")`. Он возвращает итератор по `Result<DirEntry, walkdir::Error>`.

Ключевые методы у `DirEntry`:

- **`.path()`** - возвращает `&Path` к текущему элементу.
    
- **`.file_name()`** - имя файла или папки в формате `&OsStr`.
    
- **`.file_type()`** - проверки типа элемента (`is_file()`, `is_dir()`, `is_symlink()`).
    
- **`.depth()`** - текущая глубина вложенности relative to root (0 = сама стартовая директория).
    
- **`.metadata()`** - получение размера и прав элемента «на лету».
    

## 2. Примеры использования

### A. Базовый рекурсивный обход с защитой от ошибок

_Игнорирует ошибки доступа (например, защищённые системные папки) и не валит приложение._



```Rust
use walkdir::WalkDir;

fn main() {
    for entry in WalkDir::new("/home/mbkchel")
        .into_iter()
        .filter_map(|e| e.ok()) // Мягко пропускаем ошибочные пути
    {
        println!("{}", entry.path().display());
    }
}
```

### B. Сканирование аудиотреков для плейлистов (Стиль SysPMF)

_Фильтрация каталогов и отбор файлов по расширениям (`.mp3`, `.flac`, `.wav`)._



```Rust
use walkdir::WalkDir;

fn main() {
    let music_dir = "/home/mbkchel/Music";

    let audio_files: Vec<_> = WalkDir::new(music_dir)
        .into_iter()
        .filter_map(|e| e.ok())
        .filter(|e| e.file_type().is_file()) // Исключаем сами папки
        .filter(|e| {
            e.path()
                .extension()
                .and_then(|ext| ext.to_str())
                .map(|ext| matches!(ext, "mp3" | "flac" | "wav"))
                .unwrap_or(false)
        })
        .collect();

    for file in audio_files {
        println!("Найден трек: {:?}", file.path());
    }
}
```

### C. Пропуск тяжелых/скрытых папок (`filter_entry`)

_Метод `filter_entry` игнорирует подпапки до входа в них, экономит ресурсы._



```Rust
use walkdir::{DirEntry, WalkDir};

fn is_ignored(entry: &walkdir::DirEntry) -> bool {
    let name = entry.file_name().to_str().unwrap_or("");

    // 1. Пропускаем системные указатели корня
    if name == "." || name == ".." {
        return false;
    }

    // 2. Игнорируем точечные (скрытые) файлы и мусорные директории
    name.starts_with('.') || name == "target" || name == "node_modules"
}

fn main() {
    for entry in WalkDir::new(".")
        .into_iter()
        .filter_entry(|e| !is_ignored(e)) // Пропускает всю ветку
        .filter_map(|e| e.ok())
    {
        if entry.file_type().is_file() {
            println!("Файл: {:?}", entry.path());
        }
    }
}
```

### D. Алфавитная сортировка файлов перед выдачей



```Rust
use walkdir::WalkDir;

fn main() {
    for entry in WalkDir::new("/home/mbkchel/Music")
        .sort_by_file_name() // Сортировка на каждом уровне дерева
        .into_iter()
        .filter_map(|e| e.ok())
        .filter(|e| e.file_type().is_file())
    {
        println!("Элемент: {:?}", entry.file_name());
    }
}
```

### E. Подсчет общего размера папки



```Rust
use walkdir::WalkDir;

fn get_dir_size(path: &str) -> u64 {
    WalkDir::new(path)
        .into_iter()
        .filter_map(|e| e.ok())
        .filter(|e| e.file_type().is_file())
        .filter_map(|e| e.metadata().ok())
        .map(|meta| meta.len()) // Размер в байтах
        .sum()
}

fn main() {
    let size_bytes = get_dir_size("./src");
    println!("Размер папки: {} байт", size_bytes);
}
```

## 3. Главные фишки для заметки

- **`filter_map(|e| e.ok())`** - идиоматичный фильтр для разворачивания `Result<DirEntry, Error>` без паники (`panic!`).
    
- **`filter_entry(|e| ...)`** - отсекает ветки директорий заранее, предотвращая лишнее чтение диска.
    
- **`.max_depth(n)`** - ограничивает глубину обхода (полезно для быстрых сканов).
    
- **`.sort_by_file_name()`** - сортирует элементы алфавитно на лету.
    

## 4. Где использовать?

- **Поиск и индексация медиафайлов** (сканирование библиотек в CLI-плеерах).
    
- **Системные анализаторы** (подсчет размера каталогов, поиск логов).
    
- **Парсеры исходного кода** (сбор всех файлов с расширением `.rs`).
