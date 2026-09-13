`HashMap<K, V>` - ассоциативный массив (хеш-таблица) из `std::collections`, хранящий данные в виде пар **«Ключ (`K`) -> Значение (`V`)»** в динамической памяти (куче).

**Зачем нужен?**

- **Мгновенный доступ:** Поиск элемента по строке, ID или уникальному значению за $O(1)$ без полного перебора.
    
- **Агрегация:** Подсчёт количества, группировка данных, хранение конфигураций и кэша.
    

### ⚖️ Плюсы и Минусы

|**Плюсы ➕**|**Минусы ➖**|
|---|---|
|**Высокая скорость ($O(1)$):** Поиск, вставка и удаление работают за константное время.|**Выделение памяти в куче:** Работает медленнее stack-массивов из-за аллокаций.|
|**Удобный API (`entry`):** Безопасное изменение и вставка данных без двойного поиска.|**Хаотичный порядок:** Элементы не сохраняют порядок добавления.|
|**Строгая типизация:** Полный контроль компилятора над типами ключа и значения.|**Требования к ключу:** Ключ `K` обязан реализовывать трейты `Eq` + `Hash`.|

### 🛠 Базовый CRUD (Синтаксис)

Для работы всегда нужен импорт: `use std::collections::HashMap;`

**1. Создание**



```Rust
// Пустая карта с автоматическим выводом типов
let mut map: HashMap<&str, i32> = HashMap::new();

// С заранее выделенной памятью под 100 элементов (оптимизация)
let mut fast_map = HashMap::with_capacity(100);
```

**2. Добавление и Обновление (`insert`)**



```Rust
let mut map = HashMap::new();

// Вставка новой пары
map.insert("Arch", 100);

// Перезапись: если ключ "Arch" есть, вернёт старое значение (Some(100)) и запишет 200
let old_val = map.insert("Arch", 200); 
```

**3. Чтение (`get`)**

Возвращает `Option<&V>`, так как ключа может не быть в карте.



```Rust
let mut map = HashMap::new();
map.insert("SysPrint", 11);

// Вариант 1: Безопасное извлечение через if let
if let Some(stars) = map.get("SysPrint") {
    println!("Звёзд: {stars}");
}

// Вариант 2: Достать значение или взять дефолтное
let stars = map.get("SysPrint").copied().unwrap_or(0);
```

**4. Проверка и Удаление (`contains_key`, `remove`)**



```Rust
let mut map = HashMap::new();
map.insert("AX200", "WiFi");

// Проверка наличия
if map.contains_key("AX200") {
    // Удаление (возвращает Option<V> с удаленным значением)
    let removed = map.remove("AX200");
}
```

### ⚡ Атомарная работа через `.entry()` API

Избавляет от ручной проверки «есть ли ключ, если нет - добавь».



```Rust
use std::collections::HashMap;

fn main() {
    let mut stats = HashMap::new();

    // 1. Вставить 100, ТОЛЬКО если ключа "stars" ЕЩЁ НЕТ
    stats.entry("stars").or_insert(100);
    stats.entry("stars").or_insert(500); // Проигнорируется, так как ключ уже существует

    // 2. Использование в качестве счётчика
    let text = "rust arch rust linux arch rust";
    let mut counter = HashMap::new();

    for word in text.split_whitespace() {
        // or_insert() возвращает &mut V (мутабельную ссылку)
        let count = counter.entry(word).or_insert(0);
        *count += 1; // Разыменовываем и увеличиваем
    }
    // Итог: {"rust": 3, "arch": 2, "linux": 1}
}
```

### ⚠️ Владение Памятью (Ownership)

- **Типы с `Copy`** (`i32`, `f64`, `bool`): Копируются в карту, оригиналы остаются доступны.
    
- **Владеемые типы** (`String`, `Vec<T>`): **Перемещаются (`move`)** внутрь карты!
    



```Rust
let key = String::from("GPU");
let val = String::from("RTX 4060 Ti");

let mut hardware = HashMap::new();
hardware.insert(key, val);

// println!("{key}"); // ❌ Ошибка! Переменная key перемещена в HashMap.
```

> **Решение:** Используй заимствование `HashMap<&str, &str>` или передавай `.clone()`.

### 🖥 Вывод HashMap в консоль



```Rust
use std::collections::HashMap;

fn main() {
    let mut map = HashMap::new();
    map.insert("Rust", "1.80");
    map.insert("Kernel", "6.10");

    // 1. Отладочный вывод всей карты (одной строкой)
    println!("{:?}", map);

    // 2. Форматированный многострочный отладочный вывод
    println!("{:#?}", map);

    // 3. Вывод в цикле (обязательно используем &map, чтобы не сжечь владение!)
    for (key, val) in &map {
        println!("{key} => {val}");
    }
    // 4. Вывод конкретно по ключу
    
    let mut map_1 = HashMap::new();
    map_1.insert(10, "Intel AX200");

    // Вариант 1: Через unwrap_or (если ключа нет, выведет дефолтный текст)
    println!("Сетевуха: {}", map_1.get(&10).unwrap_or(&"netu"));
```

### 📌 Шпаргалка

- `use std::collections::HashMap;` - импорт.
    
- `.insert(k, v)` - добавить / перезаписать.
    
- `.get(&k)` - читать (`Option<&V>`).
    
- `.entry(k).or_insert(v)` - безопасно обновить / инициализировать.
    
- `.remove(&k)` - удалить.
    
- `for (k, v) in &map` - перебрать без потери владения.
