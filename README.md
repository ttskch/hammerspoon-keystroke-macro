# hammerspoon-keystroke-macro

[English](./README.md) | [日本語](./README.ja.md)

A simple keystroke macro module for [Hammerspoon](https://www.hammerspoon.org).
Record a sequence of keystrokes and replay them with a hotkey.

## Features

- Toggle-style recording (same hotkey starts/stops)
- Automatically trims the recording/stopping hotkey itself from the captured stream
- Replays events at a fixed interval, so playback speed is not affected by your typing speed

## Requirements

- macOS
- [Hammerspoon](https://www.hammerspoon.org)
- Hammerspoon granted "Accessibility" permission (System Settings > Privacy & Security > Accessibility)

## Installation

```sh
curl --create-dirs -o ~/.hammerspoon/keystroke_macro/init.lua \
  "https://raw.githubusercontent.com/ttskch/hammerspoon-keystroke-macro/main/keystroke_macro/init.lua?$(date +%s)"
```

The `?$(date +%s)` suffix bypasses GitHub's CDN cache so you always get the latest version.

## Configuration

Add the following to your `~/.hammerspoon/init.lua`:

```lua
require('keystroke_macro').setup()
```

This registers the default hotkeys:

- `Cmd + Shift + Option + R` — toggle recording start/stop
- `Cmd + Shift + Option + P` — replay the recorded macro (hold to replay repeatedly)

To use custom hotkeys, pass them to `setup()`:

```lua
require('keystroke_macro').setup({
    toggleHotkey = {{"ctrl", "alt"}, "F1"},
    playHotkey   = {{"ctrl", "alt"}, "F2"},
})
```

Then click the Hammerspoon menu bar icon and choose `Reload Config` to apply.

## Usage

With the default configuration:

| Action | Hotkey |
|---|---|
| Toggle recording | `Cmd + Shift + Option + R` |
| Replay (hold to repeat) | `Cmd + Shift + Option + P` |

### Example

1. Press `Cmd + Shift + Option + R` → "● REC" appears
2. Type the keystrokes you want to record (e.g. boilerplate text, cursor movements, edits)
3. Press `Cmd + Shift + Option + R` again → "■ Saved" appears
4. Move the cursor elsewhere
5. Press `Cmd + Shift + Option + P` → the recorded sequence is replayed

Recording again overwrites the previous macro.

## API

| Function | Description |
|---|---|
| `setup(opts)` | Register hotkeys. `opts.toggleHotkey` and `opts.playHotkey` override defaults |
| `toggle()` | Start / stop recording (toggle). Recording again overwrites the previously saved macro |
| `play()` | Replay the saved macro |

## Customization

You can tune behavior via the constants at the top of `keystroke_macro/init.lua`.

| Constant | Default | Description |
|---|---|---|
| `TRIM_WINDOW` | `0.15` (sec) | Threshold for trimming hotkey-originated events at the start/end of a recording. Increase if hotkey events are accidentally captured |
| `REPLAY_INTERVAL_US` | `3000` (μs) | Interval between posted events during replay. Smaller is faster but increases the risk of dropped modifier state. Raise to `5000`-`10000` for slower targets like Slack or IME-based input |

## Known Limitations

- Macros are **lost when Hammerspoon restarts** (memory-only storage)
- IME-driven input may not replay reliably in some environments (IME events live on a different layer)
- Independent from Vim mode macros. Using both may be confusing

## License

[MIT](./LICENSE)
