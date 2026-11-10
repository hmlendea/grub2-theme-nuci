# Data Model — GRUB2 Theme Nuci

This document describes the theme descriptor format, asset declarations, layout parameters, and colour/font specifications.

## Theme Descriptor Format

**File:** `Nuci/theme.txt`
**Format:** GRUB2 theme specification — key-value pairs and named blocks
**Encoding:** UTF-8
**Parsing:** GRUB2 built-in parser at boot time

### Grammar Overview

```
theme := { global-property } [ block ]*
global-property := key ': ' value
block := '+ ' name '{' { property } '}'
property := key ' = ' value
value := string | number | percentage | colour | font-spec
```

Comments: Lines starting with `#` are ignored.

## Global Properties

| Key | Value Type | Example | Description |
|-----|------------|---------|-------------|
| `title-text` | String | `""` | Title text (empty = none) |
| `desktop-image` | String (path) | `"background.png"` | Background image file |
| `desktop-color` | Colour | `"#000000"` | Fallback background colour |
| `terminal-font` | Font spec | `"Unifont Regular 16"` | Terminal font face and size |
| `terminal-box` | String (glob) | `"terminal_box_*.png"` | 9-slice terminal box pattern |
| `terminal-left` | Number/Percentage | `"0"` | Terminal left position |
| `terminal-top` | Number/Percentage | `"0"` | Terminal top position |
| `terminal-width` | Percentage | `"100%"` | Terminal width |
| `terminal-height` | Percentage | `"100%"` | Terminal height |
| `terminal-border` | Number | `"0"` | Terminal border width |

## Boot Menu Block (`+ boot_menu`)

```text
+ boot_menu {
  left = 36%
  top = 30%
  width = 28%
  height = 60%
  item_font = "Unifont Regular 16"
  item_color = "#cccccc"
  selected_item_color = "#ffffff"
  item_height = 32
  item_spacing = 16
  selected_item_pixmap_style = "select_*.png"
}
```

| Property | Type | Description |
|----------|------|-------------|
| `left` | Percentage | Horizontal position of menu |
| `top` | Percentage | Vertical position of menu |
| `width` | Percentage | Menu width |
| `height` | Percentage | Menu height |
| `item_font` | Font spec | Font for menu items |
| `item_color` | Colour | Unselected item colour |
| `selected_item_color` | Colour | Selected item colour |
| `item_height` | Number (pixels) | Height of each menu item |
| `item_spacing` | Number (pixels) | Vertical spacing between items |
| `selected_item_pixmap_style` | Glob pattern | Selection sprite pattern (8-directional) |

## Label Block (`+ label`)

```text
+ label {
  top = 82%
  left = 35%
  width = 30%
  align = "center"
  id = "__timeout__"
  text = "Booting in %d seconds"
  color = "#cccccc"
  font = "Unifont Regular 16"
}
```

| Property | Type | Description |
|----------|------|-------------|
| `top` | Percentage | Vertical position |
| `left` | Percentage | Horizontal position |
| `width` | Percentage | Label width |
| `align` | String | Text alignment (`left`, `center`, `right`) |
| `id` | String | Internal identifier (`__timeout__` = special) |
| `text` | String | Format string (`%d` = seconds remaining) |
| `color` | Colour | Text colour |
| `font` | Font spec | Font face and size |

## Value Types

### Colours
- **Format:** `#RRGGBB` (hexadecimal, 24-bit RGB)
- **Examples:** `#000000` (black), `#ffffff` (white), `#cccccc` (light grey)
- **No alpha channel** — GRUB2 theme spec does not support transparency in colours

### Font Specifications
- **Format:** `"Family Style Size"`
- **Example:** `"Unifont Regular 16"`
- **Family:** Font family name (must be available to GRUB2)
- **Style:** `Regular`, `Bold`, `Italic`, `Bold Italic`
- **Size:** Point size (integer)

### Percentages
- **Format:** `N%` where N is integer
- **Reference:** Percentage of screen width (horizontal) or height (vertical)
- **Examples:** `36%`, `100%`

### Numbers
- **Format:** Integer (pixels unless otherwise noted)
- **Examples:** `32`, `16`, `0`

### Strings
- **Format:** Double-quoted, UTF-8
- **Escaping:** Standard C-style (`\n`, `\t`, `\"`, `\\`)
- **Glob patterns:** `*` matches any sequence, `?` matches single character

## Asset Declarations

### Background
```text
desktop-image: "background.png"
```
- **Referenced by:** Global property
- **Required:** Yes
- **Format:** PNG
- **Dimensions:** No strict requirement; GRUB2 scales to screen

### Selection Sprites (8-directional)
```text
selected_item_pixmap_style = "select_*.png"
```
- **Referenced by:** `boot_menu` block property
- **Pattern expands to:** `select_c.png`, `select_n.png`, `select_ne.png`, `select_e.png`, `select_se.png`, `select_s.png`, `select_sw.png`, `select_w.png`
- **Required:** All 8 files must exist
- **Format:** PNG with transparency
- **Purpose:** Rendered around selected menu item based on cursor direction

### Terminal Box Sprites (9-slice)
```text
terminal-box: "terminal_box_*.png"
```
- **Referenced by:** Global property
- **Pattern expands to:** `terminal_box_c.png`, `terminal_box_n.png`, `terminal_box_ne.png`, `terminal_box_e.png`, `terminal_box_se.png`, `terminal_box_s.png`, `terminal_box_sw.png`, `terminal_box_w.png`, `terminal_box_nw.png`
- **Required:** All 9 files must exist
- **Format:** PNG with transparency
- **Purpose:** 9-slice scaling for terminal window frame

### Icons
- **Referenced by:** Implicit — GRUB2 matches boot entry class to icon filename
- **Location:** `icons/{class}.png`
- **Format:** PNG, square recommended
- **Classes:** Distribution identifiers (e.g., `archlinux`, `debian`, `fedora`, `ubuntu`, `windows`, `uefi`, `memtest`, `shutdown`, `reboot`, `linux`, `unknown`)
- **Fallback:** GRUB2 uses generic icon if class-specific icon missing

## Layout Coordinate System

- **Origin:** Top-left of screen (0%, 0%)
- **X-axis:** Horizontal, increases rightward (percentage of screen width)
- **Y-axis:** Vertical, increases downward (percentage of screen height)
- **Percentages:** Relative to screen resolution at boot time
- **Pixels:** Absolute pixels (used for item_height, item_spacing, terminal-border)

## Colour Palette (Current Theme)

| Role | Colour | Hex |
|------|--------|-----|
| Background fallback | Black | `#000000` |
| Menu item (unselected) | Light grey | `#cccccc` |
| Menu item (selected) | White | `#ffffff` |
| Timeout label | Light grey | `#cccccc` |

## Font Stack

| Role | Font Spec |
|------|-----------|
| Terminal | `Unifont Regular 16` |
| Boot menu items | `Unifont Regular 16` |
| Timeout label | `Unifont Regular 16` |

**Note:** Unifont is a GNU font with extensive Unicode coverage, commonly available in GRUB2 environments.

## Validation Rules

1. **All referenced assets exist** — Every path/pattern in `theme.txt` must resolve to file(s) in `Nuci/`
2. **No external references** — No absolute paths, `../`, or URLs
3. **Glob patterns complete** — `select_*.png` → 8 files; `terminal_box_*.png` → 9 files
4. **Colour format valid** — `#RRGGBB` only
5. **Font spec parseable** — `"Family Style Size"` format
6. **Percentages 0-100%** — Layout positions and dimensions within bounds
7. **Block syntax correct** — Matching braces, valid property names

## Extensibility

New properties can be added per GRUB2 theme specification. Unknown properties are ignored by GRUB2 (forward compatibility).

To add a new boot entry icon:
1. Add `{class}.png` to `Nuci/icons/`
2. Ensure boot entry in GRUB2 config has matching `class` field
3. No `theme.txt` modification required