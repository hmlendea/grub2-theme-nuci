# Input Handling — GRUB2 Theme Nuci

Describes how user input is processed and how the theme responds.

## Input Sources

| Source | Keys | Context |
|--------|------|---------|
| Keyboard | `↑`, `↓`, `Enter`, `c`, `e`, `Esc`, `Ctrl+x`, `F10` | Menu navigation, command line, edit mode |
| Serial Console | Same as keyboard (via GRUB2 serial) | Headless/remote management |
| Timeout | Automatic (1 Hz) | Countdown label update |

## Input Processing Flow

```
Key Press → GRUB2 Core → Widget Event → Theme Callback → Re-render
```

GRUB2 core handles input; theme widgets receive events via callbacks.

## Menu Navigation

### Up/Down Arrow Keys

**Event:** `GRUB_MENU_EVENT_UP` / `GRUB_MENU_EVENT_DOWN`

**Theme Response:**
1. Update selected item index
2. Invalidate selection highlight region
3. Re-render selection highlight at new position
4. Swap buffers

**Visual:** Selection highlight sprites move to new item instantly.

### Enter Key

**Event:** `GRUB_MENU_EVENT_ENTER`

**Theme Response:**
1. Signal GRUB2 to boot selected entry
2. Clear screen (or show boot messages)
3. Theme no longer active

**Visual:** Immediate transition to kernel load.

### 'c' Key — Command Line

**Event:** `GRUB_MENU_EVENT_COMMANDLINE`

**Theme Response:**
1. Activate terminal box widget
2. Calculate terminal box bounds
3. Render 9-slice frame (`terminal_box_*.png`)
4. Show text cursor
5. Enter command line input loop

**Visual:** Terminal box frame appears at configured position.

### 'e' Key — Edit Mode

**Event:** `GRUB_MENU_EVENT_EDIT`

**Theme Response:**
1. Activate terminal box widget
2. Populate with selected entry's boot commands
3. Render 9-slice frame
3. Show text cursor at end of first line
4. Enter edit input loop

**Visual:** Terminal box with editable boot commands.

### Escape Key

**Event:** `GRUB_MENU_EVENT_ESCAPE`

**Context-Dependent Response:**
- In command line: Return to main menu
- In edit mode: Discard changes, return to main menu
- In submenu: Return to parent menu

**Theme Response:** Deactivate terminal box; re-render main menu.

## Command Line Input Loop

**Active Widget:** Terminal box

**Input Handling:** GRUB2 core line editor

**Theme Role:**
- Render terminal box frame (9-slice)
- Render text content inside frame
- Render cursor (blinking block/underline)
- Handle scroll if content exceeds box height

**Keys:**
- Printable chars: Insert at cursor
- `Backspace`/`Delete`: Delete char
- `←`/`→`: Move cursor
- `Home`/`End`: Line start/end
- `↑`/`↓`: History navigation
- `Tab`: Completion
- `Enter`: Execute command
- `Esc`: Exit to menu

## Edit Mode Input Loop

**Active Widget:** Terminal box (with pre-populated content)

**Input Handling:** GRUB2 core line editor (multi-line)

**Theme Role:** Same as command line

**Additional Keys:**
- `Ctrl+x` / `F10`: Boot with edited commands
- `Esc`: Cancel, return to menu

## Timeout Handling

**Event:** Timer tick (1 Hz)

**Theme Response:**
1. Decrement timeout counter
2. Update label text: `"Booting in %d seconds"`
3. Re-render label widget only (dirty rect)
4. At 0: GRUB2 boots default entry (theme unloaded)

**Visual:** Countdown updates once per second.

## Serial Console Input

**Mechanism:** GRUB2 serial terminal driver

**Key Mapping:** Same as keyboard (VT100 sequences)

**Theme Impact:** None; theme renders to framebuffer regardless of input source.

**Note:** Serial console users see text output; theme graphics not visible over serial.

## Input State Machine

```
┌─────────────┐
│  MAIN MENU  │ ← Initial state
└──────┬──────┘
       │ ↑/↓
       ▼
┌─────────────┐     Enter      ┌─────────────┐
│  NAVIGATING │ ─────────────→ │   BOOTING   │ (theme done)
└──────┬──────┘                └─────────────┘
       │ 'c'
       ▼
┌─────────────┐     Esc        ┌─────────────┐
│ COMMAND LINE│ ◀───────────── │  MAIN MENU  │
└──────┬──────┘                └─────────────┘
       │ 'e'
       ▼
┌─────────────┐     Esc        ┌─────────────┐
│  EDIT MODE  │ ◀───────────── │  MAIN MENU  │
└──────┬──────┘                └─────────────┘
       │ Ctrl+x/F10
       ▼
┌─────────────┐
│   BOOTING   │
└─────────────┘
```

## Theme-Specific Input Behaviour

### Selection Highlight Animation

**Current:** Instant (no animation)

**Potential:** GRUB2 supports `animation` property for widgets; not used in Nuci.

### Icon Highlight

**Current:** Icon does not change on selection

**Potential:** Could provide `select_{class}.png` variants; not implemented.

### Keyboard Hints

**Current:** No on-screen key hints

**Potential:** Could add label with key legend; not implemented.

## Accessibility Considerations

| Aspect | Status |
|--------|--------|
| High contrast | Colours configurable in theme.txt |
| Keyboard-only | Full navigation via keyboard |
| Screen reader | Not applicable (pre-OS, no AT) |
| Timeout visibility | Configurable label |
| Focus indicator | Selection highlight (8-sprite) |

## Related Documents

- [boot-sequence.md](./boot-sequence.md) — When input is active
- [rendering.md](./rendering.md) — Re-rendering on input
- [troubleshooting.md](../troubleshooting.md) — Input-related issues
- [integrations.md](../integrations.md#grub2-bootloader) — GRUB2 input handling