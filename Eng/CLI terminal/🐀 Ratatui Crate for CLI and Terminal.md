## 🎯 What it is and why you need it

**Ratatui** is a Rust library (crate) for layout and rendering of text-based user interfaces (TUIs) right in the console.

* **Why you need it:** It allows you to turn a regular text-based console utility into a full-fledged application with windows, borders, dynamic lists, volume sliders, progress bars, and tabs (like `htop`, `btop`, `neovim`, or `cmus`).
* **How it works:** Ratatui **does not read** keypresses and **does not manage** the terminal itself. It is responsible **only for rendering** (drawing pixel-characters). To work with the screen and keyboard, it uses a backend - most commonly **Crossterm**.

## 🧱 4 Architectural Pillars

Any TUI built with Ratatui relies on four core concepts:

1. **`Terminal`** - a replacement for standard `stdout`. This is the canvas that controls the window size and manages frame rendering.
2. **`Frame`** - the current frame. Passed into the closure during rendering. We place widgets onto it.
3. **`Layout`** - the grid/layout. Splits the screen (or a part of it) into rectangular regions (`Rect`).
4. **`Widgets`** - ready-made graphical components (bordered blocks, lists, progress bars, text).

## 🛠 Dependency Setup (`Cargo.toml`)

```toml
[dependencies]
ratatui = "0.26"
crossterm = "0.27"
```

## ⚙️ Step-by-Step Guide

### 1. Terminal Setup (Raw Mode + Alternate Screen)

To turn the terminal into a TUI application, it must be switched to "raw" mode (Raw Mode).

* **Raw Mode:** Keys are captured instantly (no need to wait for Enter), and typed characters are not automatically printed to the screen.
* **Alternate Screen:** Switches the console to a clean virtual screen so that after exiting the application, all user command history remains intact.

```rust
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

### 2. Screen Layout (Layout & Constraints)

The screen is split vertically (`Vertical`) or horizontally (`Horizontal`).

```rust
use ratatui::layout::{Layout, Direction, Constraint, Rect};

fn create_layout(area: Rect) -> Vec<Rect> {
    Layout::default()
        .direction(Direction::Vertical)
        .constraints([
            Constraint::Length(3),      // Fixed: 3 lines for the header
            Constraint::Min(0),         // Flexible: all remaining space for content
            Constraint::Length(3),      // Fixed: 3 lines for the status bar
        ])
        .split(area)
}
```

* `Constraint::Length(n)` - fixed size of $n$ lines/columns.
* `Constraint::Percentage(n)` - percentage of the current window.
* `Constraint::Min(n)` / `Max(n)` - adaptive size with bounds.

### 3. Essential Widgets

#### **A. Block (Borders and Title)**

The base wrapper widget for creating containers.

```rust
use ratatui::widgets::{Block, Borders, BorderType};
use ratatui::style::{Style, Color};

let block = Block::default()
    .title(" 🎵 SysPMF ")
    .borders(Borders::ALL)
    .border_type(BorderType::Rounded) // Rounded corners
    .style(Style::default().fg(Color::Cyan));
```

#### **B. Paragraph (Text)**

Simple rendering of text or statuses.

```rust
use ratatui::widgets::Paragraph;

let text = Paragraph::new("Playing: MDMA.mp3")
    .block(block);
```

#### **C. List + ListState (Interactive List)**

Used for track lists or files. `ListState` holds the index of the selected item.

```rust
use ratatui::widgets::{List, ListItem, ListState};

// Rendering an interactive list:
let items = vec![ListItem::new("Track 1"), ListItem::new("Track 2")];
let list = List::new(items)
    .highlight_symbol("> ") // Selected item marker
    .highlight_style(Style::default().fg(Color::Yellow));

// Rendered via render_stateful_widget!
frame.render_stateful_widget(list, area, &mut state.list_state);
```

#### **D. Gauge (Progress Bar)**

For displaying volume or track progress.

```rust
use ratatui::widgets::Gauge;

let progress = Gauge::default()
    .block(Block::default().title("Volume"))
    .percent(70) // Value from 0 to 100
    .gauge_style(Style::default().fg(Color::Green));
```

### 4. Application Lifecycle Loop

The primary application pattern:

```rust
fn main() -> Result<()> {
    let mut terminal = setup_terminal()?;
    
    loop {
        // 1. Frame rendering
        terminal.draw(|frame| {
            let chunks = create_layout(frame.size());
            
            let header = Paragraph::new("SysPMF Player").block(Block::default().borders(Borders::ALL));
            frame.render_widget(header, chunks[0]);
        })?;

        // 2. Non-blocking key input (Crossterm)
        if crossterm::event::poll(std::time::Duration::from_millis(50))? {
            if let crossterm::event::Event::Key(key) = crossterm::event::read()? {
                if key.code == crossterm::event::KeyCode::Char('q') {
                    break; // Exit program
                }
            }
        }
    }

    restore_terminal()?;
    Ok(())
}
```

## ⚠️ Important Nuances and Pitfalls

1. **Panic Handling:** If the program crashes (`panic!`) while the terminal is still in Raw Mode, the console will "break" (stop showing typed input). Always restore the terminal on exit or use `std::panic::set_hook`.
2. **Performance:** `terminal.draw()` calculates the difference (diff) between current and previous frames. **Only changed characters are redrawn**, so Ratatui applications put almost no load on the CPU.
3. **State Separation:** Keep player logic and state (`Volume`, `TrackList`, `CurrentTrackIndex`) in a separate `App` struct, and make rendering functions pure - they should simply take `App` and draw it onto the `Frame`.
