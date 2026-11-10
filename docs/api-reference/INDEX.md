# API Reference Index — GRUB2 Theme Nuci

This directory documents the theme descriptor format and asset specifications as the "API" consumed by GRUB2.

## Documents

| Document | Description |
|----------|-------------|
| [theme-descriptor.md](./theme-descriptor.md) | Complete `theme.txt` grammar, properties, blocks, and value types |
| [asset-specifications.md](./asset-specifications.md) | PNG requirements, dimensions, naming conventions for all asset types |
| [icon-mapping.md](./icon-mapping.md) | Boot entry class ↔ icon filename mapping rules |

## Overview

The "API" of this theme package is the contract between the static assets and the GRUB2 theme engine. It consists of:

1. **Theme Descriptor** (`theme.txt`) — Declarative configuration parsed by GRUB2
2. **Raster Assets** — PNG files referenced by the descriptor
3. **Icon Discovery** — Convention-based mapping from boot entry `class` to icon filename

There are no programmatic APIs, no functions, no classes, and no runtime interfaces. The API is entirely declarative and file-based.

## Quick Reference

### Theme Descriptor Structure

```
# Global properties
key: value

# Blocks
+ block_name {
    property = value
    ...
}
```

### Key Global Properties

| Property | Type | Example |
|----------|------|---------|
| `background` | string (path) | `"background.png"` |
| `font` | string | `"Unifont Regular 16"` |
| `color_normal` | colour | `#cccccc` |
| `color_highlight` | colour | `#ffffff` |
| `terminal_box` | glob pattern | `"terminal_box_*.png"` |
| `select` | glob pattern | `"select_*.png"` |

### Key Blocks

| Block | Purpose |
|-------|---------|
| `boot_menu` | Menu layout, positioning, item styling |
| `label` | Text labels (timeout, etc.) |
| `progress_bar` | Boot progress bar (if used) |

### Asset Naming Conventions

| Asset Type | Pattern | Count |
|------------|---------|-------|
| Background | `background.png` | 1 |
| Selection sprites | `select_{c,n,ne,e,se,s,sw,w}.png` | 8 |
| Terminal box sprites | `terminal_box_{c,n,ne,e,se,s,sw,w,nw}.png` | 9 |
| Icons | `icons/{class}.png` | Variable |

### Value Types

| Type | Format | Example |
|------|--------|---------|
| Colour | `#RRGGBB` | `#cccccc` |
| Font | `"Family Style Size"` | `"Unifont Regular 16"` |
| Percentage | `N%` | `36%` |
| Number (pixels) | integer | `32` |
| String | `"text"` | `"Booting in %d seconds"` |
| Path | `"relative/path.png"` | `"background.png"` |

## Related Documents

- [data-model.md](../data-model.md) — Detailed data model with validation rules
- [components/theme-package.md](../components/theme-package.md) — Component deep-dive
- [compatibility-contracts.md](../compatibility-contracts.md) — Stability contracts