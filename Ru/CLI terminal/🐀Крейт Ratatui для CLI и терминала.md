
## 🎯 Что это такое и зачем нужно

**Ratatui** - это Rust-библиотека (крейт) для верстки и отрисовки текстовых пользовательских интерфейсов (TUI) прямо в консоли.

- **Зачем нужен:** Позволяет превратить обычную консольную утилиту с текстом в полноценное приложение - с окнами, рамками, динамическими списками, ползунками громкости, прогресс-барами и вкладками (как `htop`, `btop`, `neovim` или `cmus`).
    
- **Как работает:** Ratatui **не считывает** нажатия клавиш и **не управляет** сам терминалом. Он отвечает **только за отрисовку** (отрисовку пикселей-символов). Для работы с экраном и клавиатурой он использует бэкенд - чаще всего **Crossterm**.
    

## 🧱 4 Архитектурных Столпа

Любой TUI на Ratatui строится на четырех концепциях:

1. **`Terminal`** - замена стандартного вывода `stdout`. Это холст, который контролирует размер окна и управляет отрисовкой кадра.
    
2. **`Frame`** - текущий кадр. Передается в замыкание при отрисовке. На нем мы размещаем виджеты.
    
3. **`Layout`** - сетка/макет. Делит экран (или его часть) на прямоугольные области (`Rect`).
    
4. **`Widgets`** - готовые графические компоненты (блоки с рамками, списки, прогресс-бары, текст).
    

## 🛠 Подключение (`Cargo.toml`)

, 

```TOML, Ini
[dependencies]
ratatui = "0.26"
crossterm = "0.27"
```

## ⚙️ Пошаговое руководство по работе

### 1. Подготовка терминала (Raw Mode + Alternate Screen)

Чтобы терминал превратился в TUI-приложение, его нужно перевести в "сырой" режим (Raw Mode).

- **Raw Mode:** Клавиши считываются мгновенно (не нужно ждать Enter), а вводимые символы не печатаются автоматически на экран.
    
- **Alternate Screen:** Переключает консоль на чистый виртуальный экран, чтобы после выхода из приложения вся история команд пользователя осталась невредимой.
    



```Rust
use crossterm::terminal::{enable_raw_mode, disable_raw_mode, EnterAlternateScreen, LeaveAlternateScreen};
use crossterm::ExecutableCommand;
use ratatui::backend::CrosstermBackend;
use ratatui::Terminal;
use std::io::{stdout, Result};

pub fn setup_terminal() -> Result<Terminal<CrosstermBackend<std::io::Stdout>>> {
    enable_raw_mode()?;
    stdout().execute(EnterAlternateScreen)?;
    Terminal::new(CrosstermBackend::new(stdout()))
}

pub fn restore_terminal() -> Result<()> {
    disable_raw_mode()?;
    stdout().execute(LeaveAlternateScreen)?;
    Ok(())
}
```

### 2. Верстка экрана (Layout & Constraints)

Экран делится по вертикали (`Vertical`) или горизонтали (`Horizontal`).



```Rust
use ratatui::layout::{Layout, Direction, Constraint, Rect};

fn create_layout(area: Rect) -> Vec<Rect> {
    Layout::default()
        .direction(Direction::Vertical)
        .constraints([
            Constraint::Length(3),      // Фиксировано: 3 строки на шапку
            Constraint::Min(0),         // Гибко: всё оставшееся место под контент
            Constraint::Length(3),      // Фиксировано: 3 строки под статус-бар
        ])
        .split(area)
}
```

- `Constraint::Length(n)` - фиксированный размер в $n$ строк/колонок.
    
- `Constraint::Percentage(n)` - процент от текущего окна.
    
- `Constraint::Min(n)` / `Max(n)` - адаптивный размер с ограничениями.
    

### 3. Основные Виджеты

#### **A. Block (Рамка и заголовок)**

Базовый виджет-обертка для создания контейнеров.



```Rust
use ratatui::widgets::{Block, Borders, BorderType};
use ratatui::style::{Style, Color};

let block = Block::default()
    .title(" 🎵 SysPMF ")
    .borders(Borders::ALL)
    .border_type(BorderType::Rounded) // Закругленные углы
    .style(Style::default().fg(Color::Cyan));
```

#### **B. Paragraph (Текст)**

Простой вывод текста или статусов.



```Rust
use ratatui::widgets::Paragraph;

let text = Paragraph::new("Playing: MDMA.mp3")
    .block(block);
```

#### **C. List + ListState (Интерактивный список)**

Используется для списка треков или файлов. `ListState` хранит индекс активного элемента.



```Rust
use ratatui::widgets::{List, ListItem, ListState};

// Отрисовка интерактивного списка:
let items = vec![ListItem::new("Track 1"), ListItem::new("Track 2")];
let list = List::new(items)
    .highlight_symbol("> ") // Маркер выбранного элемента
    .highlight_style(Style::default().fg(Color::Yellow));

// Отрисовывается через render_stateful_widget!
frame.render_stateful_widget(list, area, &mut state.list_state);
```

#### **D. Gauge (Прогресс-бар)**

Для отображения громкости или времени трека.



```Rust
use ratatui::widgets::Gauge;

let progress = Gauge::default()
    .block(Block::default().title("Volume"))
    .percent(70) // Значение от 0 до 100
    .gauge_style(Style::default().fg(Color::Green));
```

### 4. Главный цикл приложения (Lifecycle Loop)

Основной паттерн работы приложения:



```Rust
fn main() -> Result<()> {
    let mut terminal = setup_terminal()?;
    
    loop {
        // 1. Отрисовка кадра
        terminal.draw(|frame| {
            let chunks = create_layout(frame.size());
            
            let header = Paragraph::new("SysPMF Player").block(Block::default().borders(Borders::ALL));
            frame.render_widget(header, chunks[0]);
        })?;

        // 2. Неблокирующий ввод клавиш (Crossterm)
        if crossterm::event::poll(std::time::Duration::from_millis(50))? {
            if let crossterm::event::Event::Key(key) = crossterm::event::read()? {
                if key.code == crossterm::event::KeyCode::Char('q') {
                    break; // Выход из программы
                }
            }
        }
    }

    restore_terminal()?;
    Ok(())
}
```

## ⚠️ Важные нюансы и грабли

1. **Обработка Паник:** Если программа упадет (`panic!`), а терминал останется в Raw Mode - консоль "сломается" (перестанет отображать вводимый текст). Обязательно восстанавливай терминал при выходе или используй `std::panic::set_hook`.
    
2. **Производительность:** `terminal.draw()` вычисляет дифференциал (разницу) между текущим и прошлым кадром. Перерисовываются **только измененные символы**, поэтому приложения на Ratatui почти не нагружают процессор.
    
3. **Разделение состояния:** Храни логику плеера и данные (`Volume`, `TrackList`, `CurrentTrackIndex`) в отдельной структуре `App`, а функции отрисовки сделай чистыми - они должны просто брать `App` и рисовать его на `Frame`.
