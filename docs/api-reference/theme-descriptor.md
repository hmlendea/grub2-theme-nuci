# Theme Descriptor Reference — GRUB2 Theme Nuci

Complete specification of the `theme.txt` format consumed by GRUB2.

## Grammar

```
theme_file        ::= (global_property | block | comment | blank_line)*

global_property   ::= key ':' space value newline
block             ::= '+' space block_name space '{' newline block_body '}' newline
block_body        ::= (block_property | comment | blank_line)*
block_property    ::= key space '=' space value newline
comment           ::= '#' .* newline
blank_line        ::= newline

key               ::= [a-z_]+
block_name        ::= [a-z_]+
value             ::= string | colour | font | percentage | number | path | glob
string            ::= '"' [^"]* '"'
colour            ::= '#' [0-9a-fA-F]{6}
font              ::= '"' [^"]+ space [^"]+ space [^"]+ '"'
percentage        ::= [0-9]+ '%'
number            ::= [0-9]+
path              ::= '"' [^"]+ '"'
glob              ::= '"' [^"]* '*' [^"]* '"'
```

## Global Properties

| Property | Type | Required | Description |
|----------|------|----------|-------------|
| `background` | path | Yes | Background image path relative to theme.txt |
| `font` | font | Yes | Default font for all text |
| `color_normal` | colour | No | Default text colour (default: `#ffffff`) |
| `color_highlight` | colour | No | Selected item text colour (default: `#ffffff`) |
| `color_border` | colour | No | Border colour (default: `#000000`) |
| `terminal_box` | glob | Yes | Terminal box sprite glob pattern |
| `select` | glob | Yes | Selection sprite glob pattern |

## Blocks

### `boot_menu`

Configures the main boot menu appearance and layout.

| Property | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `left` | percentage | Yes | — | Horizontal position (0-100%) |
| `top` | percentage | Yes | — | Vertical position (0-100%) |
| `width` | percentage | Yes | — | Menu width (0-100%) |
| `height` | percentage | Yes | — | Menu height (0-100%) |
| `item_font` | font | No | global `font` | Font for menu items |
| `item_color` | colour | No | global `color_normal` | Unselected item text colour |
| `item_color_highlight` | colour | No | global `color_highlight` | Selected item text colour |
| `item_height` | number | No | `32` | Item height in pixels |
| `item_spacing` | number | No | `16` | Vertical spacing between items in pixels |
| `item_padding` | number | No | `0` | Horizontal padding in pixels |
| `icon_width` | number | No | `0` | Icon width (0 = auto) |
| `icon_height` | number | No | `0` | Icon height (0 = auto) |
| `icon_spacing` | number | No | `10` | Space between icon and text |

### `label`

Configures text labels (e.g., timeout countdown).

| Property | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `left` | percentage | Yes | — | Horizontal position |
| `top` | percentage | Yes | — | Vertical position |
| `width` | percentage | No | `100%` | Label width |
| `height` | percentage | No | `10%` | Label height |
| `align` | string | No | `"center"` | Text alignment: `left`, `center`, `right` |
| `font` | font | No | global `font` | Label font |
| `color` | colour | No | global `color_normal` | Label text colour |
| `text` | string | Yes | — | Label text (supports `%d` for timeout) |
| `visible` | boolean | No | `true` | Show/hide label |

### `progress_bar`

Configures boot progress bar (optional).

| Property | Type | Required | Default | Description |
|----------|------|----------|---------|-------------|
| `left` | percentage | Yes | — | Horizontal position |
| `top` | percentage | Yes | — | Vertical position |
| `width` | percentage | Yes | — | Bar width |
| `height` | percentage | Yes | — | Bar height |
| `bg_color` | colour | No | `#000000` | Background colour |
| `fg_color` | colour | No | `#ffffff` | Foreground (progress) colour |
| `border_color` | colour | No | `#ffffff` | Border colour |
| `show_text` | boolean | No | `false` | Show percentage text |

## Value Type Details

### Colour (`#RRGGBB`)

24-bit RGB hexadecimal. No alpha channel. Case-insensitive.

Examples: `#ffffff`, `#cccccc`, `#000000`, `#ff0000`

### Font (`"Family Style Size"`)

Three space-separated tokens in double quotes.

- **Family:** Font family name (e.g., `Unifont`, `DejaVu Sans`)
- **Style:** `Regular`, `Bold`, `Italic`, `Bold Italic`
- **Size:** Point size as integer (e.g., `16`)

Example: `"Unifont Regular 16"`

### Percentage (`N%`)

Integer followed by percent sign. Represents percentage of screen dimension.

Valid range: `0%` to `100%`. Values outside have undefined behaviour.

Examples: `36%`, `100%`, `0%`

### Number (pixels)

Positive integer representing pixel count.

Examples: `32`, `16`, `10`

### String (`"text"`)

UTF-8 text in double quotes. Escape sequences not supported.

Example: `"Booting in %d seconds"`

### Path (`"relative/path.png"`)

Relative path from `theme.txt` location. No `../`, no absolute paths, no URLs.

Example: `"background.png"`, `"icons/archlinux.png"`

### Glob (`"pattern*.png"`)

Glob pattern for sprite collections. Only `*` and `?` wildcards supported.

Used for: `terminal_box`, `select` properties.

Example: `"select_*.png"`, `"terminal_box_*.png"`

## Nuci Theme.txt (Reference)

```text
# GRUB2 Theme - Nuci
# Generated for grub2-theme-nuci

background: "background.png"
font: "Unifont Regular 16"
color_normal: #cccccc
color_highlight: #ffffff
terminal_box: "terminal_box_*.png"
select: "select_*.png"

+ boot_menu {
    left = 36%
    top = 30%
    width = 28%
    height = 60%
    item_font = "Unifont Regular 16"
    item_color = #cccccc
    item_color_highlight = #ffffff
    item_height = 32
    item_spacing = 16
    item_padding = 0
    icon_width = 0
    icon_height = 0
    icon_spacing = 10
}

+ label {
    left = 35%
    top = 82%
    width = 30%
    height = 10%
    align = "center"
    font = "Unifont Regular 16"
    color = #cccccc
    text = "Booting in %d seconds"
}
```

## Validation Rules

1. **Required globals:** `background`, `font`, `terminal_box`, `select`
2. **Required boot_menu:** `left`, `top`, `width`, `height`
3. **Required label:** `left`, `top`, `text`
4. **Percentages:** Must be `0%`–`100%`
5. **Colours:** Must be `#RRGGBB` format
6. **Font:** Must have three tokens
7. **Paths:** Must be relative, no `../`, no URLs
8. **Globs:** Only for `terminal_box` and `select`
9. **No duplicate keys** in same scope
10. **Block names** must be known to GRUB2 (`boot_menu`, `label`, `progress_bar`)

## GRUB2 Version Notes

| Feature | Minimum GRUB2 | Notes |
|---------|---------------|-------|
| Basic theme | 2.00 | Core theme engine |
| `progress_bar` | 2.02 | Optional |
| `icon_*` properties | 2.04 | Icon support |
| `item_padding` | 2.06 | Spacing control |
| `visible` in label | 2.12 | Show/hide labels |

## Error Handling

GRUB2 theme parsing errors:
- **Syntax error:** Boot falls back to default theme
- **Missing asset:** Widget renders without image (may be invisible)
- **Invalid colour:** Undefined behaviour (may default to white)
- **Invalid percentage:** Undefined behaviour (may clamp or ignore)
- **Unknown property:** Ignored (forward compatibility)
- **Unknown block:** Ignored

No error messages displayed at boot; failures are silent.