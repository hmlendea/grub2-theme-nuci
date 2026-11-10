# Boot Sequence Behaviour — GRUB2 Theme Nuci

Describes the theme's role and behaviour during the GRUB2 boot sequence.

## Boot Phase Overview

```
Firmware (BIOS/UEFI)
    ↓
GRUB2 Stage 1 (boot.img / bootx64.efi)
    ↓
GRUB2 Stage 2 (core.img / grubx64.efi) ← Theme loaded here
    ↓
GRUB2 Menu Display ← Theme rendered here
    ↓
User Selection / Timeout
    ↓
Kernel Load → Initramfs → Handoff
```

## Theme Loading

**When:** During GRUB2 Stage 2 initialisation, after video mode set.

**Trigger:** `GRUB_THEME` variable set in `grub.cfg` (via `/etc/default/grub` + `grub-mkconfig`).

**Process:**
1. GRUB2 reads `theme.txt` from `GRUB_THEME` path
2. Parses global properties and blocks
3. Loads referenced assets (PNGs) into video memory
4. Initialises widgets (boot_menu, label, progress_bar)
5. Renders initial frame

**Failure Modes:**
- Theme file not found → Fallback to default theme (no error shown)
- Parse error → Fallback to default theme
- Asset load error → Widget renders without image (may be invisible)
- Video mode incompatible → Undefined rendering

## Menu Display Sequence

### 1. Background Render
- `background.png` decoded and scaled to screen resolution
- Drawn as full-screen backdrop
- If missing/corrupt: solid colour (usually black)

### 2. Boot Menu Widget
- Positioned per `boot_menu` block (`left`, `top`, `width`, `height`)
- Background: transparent (shows theme background)
- Border: none by default
- Items populated from `grub.cfg` menuentries

### 3. Item Rendering (Per Entry)
For each boot entry:
1. Determine icon: match `class` → `icons/{class}.png`
2. Render icon (if found) at item left
3. Render entry title text per `item_font`, `item_color`
4. Apply selection highlight if selected

### 4. Selection Highlight
- Uses `select_*.png` 8-sprite set
- Renders around currently selected item
- Updates on ↑/↓ navigation
- `select_c` fills horizontal; corners/edges frame

### 5. Timeout Label
- Positioned per `label` block
- Text: `"Booting in %d seconds"` with countdown
- Updates once per second
- Hidden when timeout = 0 or disabled

### 6. Progress Bar (If Configured)
- Positioned per `progress_bar` block
- Updates during kernel/initramfs load
- Not used in Nuci theme (no progress_bar block)

## User Interaction Behaviour

### Keyboard Navigation

| Key | Action | Visual Feedback |
|-----|--------|-----------------|
| `↑` / `↓` | Move selection | Selection highlight moves; sprites update |
| `Enter` | Boot selected entry | Screen clears; kernel load begins |
| `c` | Enter command line | Terminal box appears (9-slice frame) |
| `e` | Edit selected entry | Edit mode with terminal box frame |
| `Esc` | Exit submenu/command line | Return to main menu |

### Command Line Mode (`c`)

1. Terminal box widget activated
2. `terminal_box_*.png` 9-slice frame rendered
3. Text input area inside frame
4. GRUB2 commands accepted
5. `Esc` returns to menu

### Edit Mode (`e`)

1. Selected entry's boot commands displayed
2. Terminal box frame rendered
3. Editable text area
4. `Ctrl+x` or `F10` boots modified entry
5. `Esc` discards changes

## Timeout Behaviour

**Configuration:** `GRUB_TIMEOUT` in `/etc/default/grub`

**Behaviour:**
- Countdown displayed in timeout label
- Updates once per second
- At 0: boots default entry automatically
- `GRUB_TIMEOUT=0`: immediate boot (no menu)
- `GRUB_TIMEOUT=-1`: wait indefinitely (no timeout)

**Theme Interaction:**
- Label text: `"Booting in %d seconds"` (from theme.txt)
- `%d` substituted with remaining seconds
- Label hidden if `visible = false` or timeout disabled

## Theme Switching

**Not supported at runtime.** Theme is loaded once at GRUB2 Stage 2 initialisation.

To change theme:
1. Modify `GRUB_THEME` in `/etc/default/grub`
2. Run `grub-mkconfig`
3. Reboot

## Multi-Boot Behaviour

Theme applies to all boot entries uniformly:
- Same background, menu, fonts for all entries
- Icons differ per entry `class`
- No per-entry theme customisation

## Firmware Differences

### BIOS (CSM)
- Video via VBE (VESA BIOS Extensions)
- Resolution set by `GRUB_GFXMODE`
- Limited to VBE-supported modes
- Theme scales to active mode

### UEFI
- Video via GOP (Graphics Output Protocol)
- Resolution set by `GRUB_GFXMODE` or firmware default
- Native resolution usually available
- Theme scales to active mode

**Theme Impact:** Identical rendering logic; only video mode setup differs.

## Shutdown/Reboot Entries

Special entries with classes `shutdown` and `reboot`:
- Icons: `icons/shutdown.png`, `icons/reboot.png`
- Selecting them triggers immediate ACPI action
- No kernel load; firmware handles power state

## Error Conditions During Boot

| Condition | Theme Behaviour |
|-----------|-----------------|
| Asset decode failure | Widget renders without image |
| Font unavailable | Falls back to built-in font |
| Video mode change | Theme re-rendered at new resolution |
| Out of video memory | Undefined (may crash GRUB2) |
| Corrupt theme.txt | Fallback to default theme |

## Performance Characteristics

| Metric | Typical Value |
|--------|---------------|
| Theme parse time | < 10 ms |
| Background decode | 10-50 ms (depends on size) |
| Sprite decode (all) | 5-20 ms |
| Initial render | < 100 ms total |
| Menu navigation | Instant (sprites in VRAM) |
| Timeout update | Once/sec; negligible |

## Related Documents

- [rendering.md](./rendering.md) — Rendering pipeline details
- [input-handling.md](./input-handling.md) — Input processing
- [troubleshooting.md](../troubleshooting.md) — Boot-time issues
- [integrations.md](../integrations.md#grub2-bootloader) — GRUB2 integration