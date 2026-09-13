## 🎯 Зачем нужен

Крейт **`dirs`** отдает правильные пути к стандартным директориям пользователя (Home, Config, Music, Documents) независимо от того, где запущен код - на Linux, Windows или macOS.

- **Главный плюс:** Избавляет от необходимости вручную прописывать хардкод вроде `/home/user/` или `C:\Users\...`.
    

## 🛠 Подключение (`Cargo.toml`)


```TOML
[dependencies]
dirs = "5.0"
```

## ⚡️ Топ-Функции (Самые важные)

Все функции возвращают `Option<PathBuf>`, так как в теории папка может отсутствовать в системе.

|**Функция**|**Linux / macOS**|**Windows**|
|---|---|---|
|**`dirs::home_dir()`**|`/home/username`|`C:\Users\Username`|
|**`dirs::audio_dir()`**|`/home/username/Music`|`C:\Users\Username\Music`|
|**`dirs::config_dir()`**|`/home/username/.config`|`C:\Users\Username\AppData\Roaming`|
|**`dirs::download_dir()`**|`/home/username/Downloads`|`C:\Users\Username\Downloads`|

## 💡 Базовый Шаблон



```Rust
use dirs;
use std::fs;

fn main() {
    // 1. Получаем домашнюю папку пользователя
    if let Some(mut path) = dirs::home_dir() {
        // 2. Добавляем к пути имя нашей папки
        path.push("SysPMF");

        // 3. Создаем папку, если её ещё нет
        if !path.exists() {
            fs::create_dir_all(&path).unwrap();
            println!("Создана папка: {:?}", path);
        }
    }
}
```
