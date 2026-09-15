**Описание:** Кроссплатформенный клейт для получения стандартных системных путей (Home, Config, Cache, Data и т.д.) в соответствие со спецификациями OS (Linux XDG, macOS, Windows).

**Cargo.toml:**

```TOML
[dependencies]
directories = "5.0" # или актуальная версия
```

## 1. Основные структуры

Клейт предоставляет три главных структуры:

1. **`BaseDirs`** - базовые пользовательские директории (`home_dir`, `config_dir`, `cache_dir`, `data_dir`).
    
2. **`UserDirs`** - стандартные папки пользователя (`audio_dir`, `document_dir`, `download_dir`, `picture_dir` и т.д.).
    
3. **`ProjectDirs`** - пути, привязанные к конкретному приложению/проекту (создаются по схеме квалификации: квалификатор, организация, имя проекта).
    

## 2. Примеры использования

### A. Получение базовых путей пользователя (`BaseDirs`)

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

### B. Пути для пользовательских файлов (`UserDirs`)



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

### C. Пути конфигов и данных проекта (`ProjectDirs`)

_Самый частый вариант для CLI-утилит и приложений._



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

        // Пример получения пути к файлу конфига:
        let config_file = config_dir.join("config.toml");
        println!("Config file path:   {:?}", config_file);
    }
}
```

## 4. Практика: Автосоздание папки и файла конфига

Метод `std::fs::create_dir_all` рекурсивно создает всю цепочку директорий, если их ещё нет на диске (если уже есть - просто ничего не делает и не выдаёт ошибку).

```Rust
use directories::UserDirs;
use std::fs;

fn main() {
    // 1. Берем базовые пользовательские папки
    if let Some(user_dirs) = UserDirs::new() {
        // 2. Формируем путь (например, ~/SysPMF)
        let path = user_dirs.home_dir().join("SysPMF");

        // 3. Создаем папку, если ее еще нет.
        // Если произойдет ошибка (например, нет прав) - выводим сообщение
        if let Err(e) = fs::create_dir_all(&path) {
            eprintln!("Error creating directory: {e}");
        }

        // 4. Работаем дальше с гарантированно существующим путем
        println!("Рабочая директория: {:?}", path);
    }
}
```

### Главные фишки для заметки:

- **`fs::create_dir_all(&path)`** - аналог `mkdir -p` в Linux/Bash. Создает весь путь целиком.
    
- **`path.exists()`** - быстрая проверка, создан ли уже файл или каталог.
    
- **`dir.join("filename")`** - безопасное склеивание путей через `PathBuf` с учетом слэшей текущей ОС (`/` или `\`).


## 4. Где использовать?

- **Хранение конфигов CLI-инструментов** (`ProjectDirs::config_dir()`).
    
- **Кэширование данных** (`ProjectDirs::cache_dir()`).
    
- **Поиск пользовательских загрузок/документов** (`UserDirs`).