### 1. `struct` - это Чертёж (Шаблон)

Структура не создаёт конкретные данные, она лишь объясняет Rust, из чего состоит объект.



```Rust
#[derive(Debug)] // <-- Магия: позволяет печатать всю структуру через {:?}
struct User {
    name: String,
    email: String,
    age: u32,
    active: bool,
}
```

### 2. Создание объектов и Фишки (Shorthand & Update)

#### 🔹 Сокращённая запись (Field Init Shorthand)

Если имена переменных совпадают с полями структуры - не нужно дублировать:



```Rust
let name = String::from("MBKCHEL");
let email = String::from("mbkchelofficialdev@gmail.com");

// Вместо name: name пишем просто name
let user1 = User {
    name,
    email,
    age: 13,
    active: true,
};
```

#### 🔹 Создание на основе другого объекта (Struct Update Syntax)

Хочешь скопировать юзера, но поменять только пару полей? Используй `..`:



```Rust
let user2 = User {
    name: String::from("BALBES"),
    email: String::from("вашпример@gmail.com"),
    ..user1 // Все остальные поля (age, active) заберутся из user1!
};
```

### 3. `impl` (Implementation) - Даём структуре "Суперсилы"

Без `impl` структура - это просто мешок с данными. С `impl` мы пишем **методы** (функции, которые принадлежит этой структуре).



```Rust
impl User {
    // 1. Конструктор (Ассоциированная функция) - создает нового юзера
    fn new(name: String, email: String) -> Self {
        Self {
            name,
            email,
            age: 13,
            active: true,
        }
    }

    // 2. Метод чтения (&self) - просто смотрит на данные
    fn print_info(&self) {
        println!("Пользователь: {}, Email: {}", self.name, self.email);
    }

    // 3. Метод изменения (&mut self) - может менять поля!
    fn make_older(&mut self) {
        self.age += 1;
        println!("С днём рождения, {}! Теперь тебе {} лет.", self.name, self.age);
    }
}
```

### 4. Кортежные структуры (Tuple Structs)

Если тебе нужна структура, но лениво придумывать названия для полей:



```Rust
struct Color(i32, i32, i32); // RGB
struct Point(i32, i32, i32); // Координаты X, Y, Z

let black = Color(0, 0, 0);
println!("Красный цвет: {}", black.0); // Доступ через индекс!
```


### 5. Пустые структуры (Unit-Like Structs)
## Структуры без единого поля. Занимают 0 байт в памяти.

```rust
struct AlwaysEqual;

let subject = AlwaysEqual;
```

### **Зачем они нужны?** Пустые структуры юзают, когда тебе не нужно хранить данные, но нужно повесить на структуру какое-то поведение через `impl` или реализовать **Трейт** (интерфейс). Например, для создания сервисов, парсеров или маркеров типов.

### 🚀 Полный рабочий пример в одном блоке:



```Rust
#[derive(Debug)]
struct Player {
    nickname: String,
    hp: u32,
}

impl Player {
    fn new(nickname: String) -> Self {
        Self { nickname, hp: 100 }
    }

    fn take_damage(&mut self, damage: u32) {
        self.hp -= damage;
        println!("{} получил {} урона! Осталось HP: {}", self.nickname, damage, self.hp);
    }
}

fn main() {
    let mut player1 = Player::new(String::from("MBKCHEL"));
    player1.take_damage(20);
    
    println!("{:#?}", player1); // Выведет всю структуру красиво!
}
```
### 6. Владение `self` в методах (3 вида приема данных)

В Rust метод сам выбирает, как ему работать с объектом:

1. `&self` - **Чтение** (Используем в 90% случаев). Не забирает владение, не может менять данные.
2. `&mut self` - **Мутация**. Позволяет менять поля структуры. Требует, чтобы сам экземпляр был объявлен через `let mut`.
3. `self` - **Забор владения (Move)**. Объект **уничтожается** внутри метода или трансформируется в что-то другое. После вызова исходная переменная умирает!

```rust
impl Player {
    // Принимает self по значению - съедает объект
    fn die(self) {
        println!("Игрок {} погиб навсегда.", self.nickname);
        // self выбывает из области видимости и очищается из памяти
    }
}
```
### 7. Множественные блоки `impl`

В Rust не обязательно пихать все методы в один блок `impl`. Их можно разбивать на несколько блоков (удобно для больших файлов или организации кода):



```Rust
impl Player {
    fn new(nickname: String) -> Self {
        Self { nickname, hp: 100 }
    }
}

// Второй блок impl для той же структуры
impl Player {
    fn heal(&mut self, amount: u32) {
        self.hp += amount;
    }
}
```
### 8. Методы с несколькими параметрами

Метод может принимать любые дополнительные аргументы, как и обычные функции:



```Rust
struct Rectangle {
    width: u32,
    height: u32,
}

impl Rectangle {
    // Проверяет, поместится ли другой прямоугольник внутри текущего
    fn can_hold(&self, other: &Rectangle) -> bool {
        self.width > other.width && self.height > other.height
    }
}

fn main() {
    let rect1 = Rectangle { width: 30, height: 50 };
    let rect2 = Rectangle { width: 10, height: 40 };

    println!("Поместится? {}", rect1.can_hold(&rect2)); // true
}
```

### 💡 Чек-лист для запоминания

- **`struct`** - хранит только **данные**.
    
- **`impl`** - хранит только **поведение** (функции и методы).
    
- Вызов через **`.`** (`rect.area()`) - для методов с `self`.
    
- Вызов через **`::`** (`String::from()`, `Player::new()`) - для ассоциированных функций без `self` (конструкторов).
