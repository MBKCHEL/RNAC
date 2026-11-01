## 🎯 What it is and why you need it
Rodio is the most popular audio library in the Rust ecosystem. It takes care of all the heavy lifting with the system audio card and sound streams, allowing you to easily load and play files (.mp3, .wav, .flac, .ogg).

Main advantage: Works "out of the box" on Linux, Windows, and macOS without needing to write low-level code for each C-library sound server (ALSA, PulseAudio, PipeWire).

## 🧱 Main Entities (How it works)
All work with rodio relies on 3 main elements:

* **OutputStream (Audio Output)** - connection to your sound card/speakers. It must stay alive as long as music is playing. If dropped (removed from memory), the sound instantly disappears.
* **Sink (Control Player / "Sink")** - the main control organ. You "feed" audio into it, and use it to pause, adjust volume, stop, or seek the track.
* **Decoder (Decoder)** - reads an audio file from disk (or memory) and turns compressed bytes (.mp3 / .ogg) into a raw stream of audio samples.

## 🛠 Dependency Setup (Cargo.toml)
```toml
[dependencies]
rodio = "0.17" # Version may vary
```

## ⚙️ Basic Code Template

```Rust
use rodio::{Decoder, OutputStream, Sink};
use std::fs::File;
use std::io::BufReader;

fn main() {
    // 1. Initialize the system audio output
    // _stream_handle is needed to connect to the sound card
    let (_stream, stream_handle) = OutputStream::try_default().unwrap();

    // 2. Create the player (control Sink)
    let player = Sink::try_new(&stream_handle).unwrap();

    // 3. Open the file and wrap it in BufReader (for fast reading)
    let file = File::open("MDMA.mp3").unwrap();
    let reader = BufReader::new(file);

    // 4. Decode the audio file
    let source = Decoder::new(reader).unwrap();

    // 5. Send the track to the Sink and start playback
    sink.append(source);

    // 6. Wait until the track finishes playing (otherwise the program exits immediately)
    sink.sleep_until_end();
}
}
```

## 🎛 Основные Команды Управления (`Sink`)

Всё управление воспроизведением происходит через методы `Sink`:

| **Метод**                  | **Что делает**                                     | **Пример**                    |
| -------------------------- | -------------------------------------------------- | ----------------------------- |
| **`sink.play()`**          | Unpause / resume playing                           | `sink.play();`                |
| **`sink.pause()`**         | Pause playback                                     | `sink.pause();`               |
| **`sink.is_paused()`**     | Check if paused (bool)                             | `if sink.is_paused() { ... }` |
| **`sink.set_volume(val)`** | Change volume (1.0 = 100%, 0.5 = 50%, 2.0 = (200%) | `sink.set_volume(0.8);`       |
| **`sink.volume()`**        | Get current volume level (f32)                     | `let vol = sink.volume();`    |
| **`sink.stop()`**          | Stop completely and clear the queue                | `sink.stop();`                |
| **`sink.empty()`**         | Check if the track queue is empty (bool)           | `if sink.empty() { ... }`     |
| **`sink.append(source)`**  | Add the next track to the queue                    | `sink.append(next_source);`   |

## ⚠️ Important Nuances and Pitfalls

1. **Do not let OutputStream get dropped:**
    
    
    ```Rust
    // ERROR: _stream will be dropped at the end of the function, and sound will stop immediately!
fn play_sound() {
    let (_stream, handle) = OutputStream::try_default().unwrap();
    let sink = Sink::try_new(&handle).unwrap();
    // ...
}
    ```
    
   **Solution:** The `_stream` variable (or the entire stream) needs to be stored in the main application struct for the entire lifecycle of the program.
    
1. **Working with volume (f32):**
    
- Volume in rodio is set using a floating-point number `f32`.
    
- `0.0` - complete silence.
    
- `1.0` - standard volume (100%).
    
- When adjusting with `+` and `-` buttons, it is convenient to increment/decrement by steps of `0.1` (10%).
        
2. Playback Queue (Playlist):

If you call `sink.append(track1)` and immediately `sink.append(track2)`, rodio will not overlap them, but will play them sequentially: first track 1, then track 2.