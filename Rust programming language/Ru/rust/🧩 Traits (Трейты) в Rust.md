**Суть:** Трейт - это контракт, описывающий общее поведение для разных типов. Аналог интерфейсов в других языках.

### 1. Базовый пример (Контракт поведения)



```Rust
// 1. Объявляем трейт (контракт)
trait Speak {
    fn speak(&self);
}

struct Dog;
struct Cat;

// 2. Реализуем трейт для Собаки
impl Speak for Dog {
    fn speak(&self) {
        println!("Гав!");
    }
}

// 3. Реализуем трейт для Кошки
impl Speak for Cat {
    fn speak(&self) {
        println!("Мяу!");
    }
}

fn main() {
    let dog = Dog;
    let cat = Cat;

    dog.speak(); // Выведет: Гав!
    cat.speak(); // Выведет: Мяу!
}
```

### 2. Дефолтная реализация (Default Implementation)

В трейте можно сразу написать готовую логику функции. Если тип её не переопределит, сработает дефолтная:



```Rust
trait Summary {
    // Обязательный метод
    fn author(&self) -> String;

    // Метод с готовой реализацией по умолчанию
    fn summarize(&self) -> String {
        format!("(Читать далее от автора {})", self.author())
    }
}

struct Article {
    author: String,
    title: String,
}

impl Summary for Article {
    fn author(&self) -> String {
        self.author.clone()
    }
    // summarize() можно не писать, сработает дефолтный!
}

fn main() {
    let article = Article {
        author: String::from("Ваня"),
        title: String::from("Rust Concurrency"),
    };

    println!("{}", article.summarize()); 
    // Выведет: (Читать далее от автора Ваня)
}
```

### 3. Trait Bounds (Ограничения типов в функциях)

Принимаем в функцию **любой** тип, лишь бы он реализовывал нужный трейт.

```Rust
trait Render {
    fn render_ui(&self);
}

struct CpuWidget;
struct DiskWidget;

impl Render for CpuWidget {
    fn render_ui(&self) { println!("[CPU] Status: OK"); }
}

impl Render for DiskWidget {
    fn render_ui(&self) { println!("[DISK] Status: OK"); }
}

// Принимает ЛЮБОЙ тип, реализующий трейт Render
fn draw_component(item: &impl Render) {
    item.render_ui();
}

fn main() {
    let cpu = CpuWidget;
    let disk = DiskWidget;

    draw_component(&cpu);
    draw_component(&disk);
}
```

### 4. Встроенные системные трейты (Компилятор делает за тебя)

Не нужно писать вручную базовые операции - используй `#[derive(...)]`:



```Rust
#[derive(Debug, Clone, PartialEq)]
struct Config {
    threads: u32,
    verbose: bool,
}

fn main() {
    let cfg1 = Config { threads: 12, verbose: true };
    let cfg2 = cfg1.clone(); // Работает благодаря Clone

    println!("{:?}", cfg1); // Работает благодаря Debug

    if cfg1 == cfg2 { // Работает благодаря PartialEq
        println!("Конфиги идентичны!");
    }
}
```

### 💡 Короткая шпаргалка

- **`trait Name { ... }`** - объявить интерфейс.
    
- **`impl Name for Type { ... }`** - привязать интерфейс к структуре.
    
- **`impl TraitName` в аргументах** - принимать любые структуры с этим трейтом.
    
- **`#[derive(Debug, Clone, PartialEq)]`** - автоматическая генерация стандартных трейтов компилятором.
