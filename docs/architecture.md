# Architecture — GRUB2 Theme Nuci

This document complements the root [ARCHITECTURE.md](../ARCHITECTURE.md) by elaborating on the theme package structure, GRUB2 integration boundary, and asset contracts specific to this repository.

## Theme Package Structure

The repository contains a single theme package under `Nuci/`:

```
Nuci/
├── theme.txt              # Theme descriptor (authoritative)
├── background.png         # Boot menu background
├── select_*.png           # 8 directional selection sprites
├── terminal_box_*.png     # 9-slice terminal box sprites
└── icons/                 # Boot entry class icons
    ├── archlinux.png
    ├── debian.png
    ├── fedora.png
    ├── ubuntu.png
    └── ... (other distribution icons)
```

### Theme Descriptor (`theme.txt`)

The `theme.txt` file is the single source of truth for the theme. It declares:

- **Global properties:** `title-text`, `desktop-image`, `desktop-color`, `terminal-font`
- **Terminal box:** `terminal-box` (9-slice pattern), `terminal-left`, `terminal-top`, `terminal-width`, `terminal-height`, `terminal-border`
- **Boot menu block (`+ boot_menu`):** Position, dimensions, fonts, colours, item styling, selection sprite pattern
- **Label block (`+ label`):** Timeout message position, styling, format string

All asset references use relative paths or glob patterns resolved within the `Nuci/` directory.

### Asset Contracts

| Asset Category | Files | Reference Pattern | Requirements |
|----------------|-------|-------------------|--------------|
| Background | `background.png` | `desktop-image: "background.png"` | PNG, recommended 16:9 or 4:3 |
| Selection sprites | `select_c.png`, `select_n.png`, `select_ne.png`, `select_e.png`, `select_se.png`, `select_s.png`, `select_sw.png`, `select_w.png` | `selected_item_pixmap_style = "select_*.png"` | 8 directional sprites, PNG |
| Terminal box sprites | `terminal_box_c.png`, `terminal_box_n.png`, `terminal_box_ne.png`, `terminal_box_e.png`, `terminal_box_se.png`, `terminal_box_s.png`, `terminal_box_sw.png`, `terminal_box_w.png`, `terminal_box_nw.png` | `terminal-box: "terminal_box_*.png"` | 9-slice scaling, PNG |
| Icons | `icons/*.png` | Implicit via GRUB2 boot entry classes | PNG, square recommended |

## GRUB2 Integration Boundary

The theme package integrates with GRUB2 at three boundaries:

### 1. Installation Boundary
- **Location:** `/usr/share/grub/themes/Nuci/`
- **Contract:** Directory structure must be preserved exactly
- **Permissions:** 755 directories, 644 files
- **Owner:** Root (installed by packager or administrator)

### 2. Configuration Boundary
- **File:** `/etc/default/grub`
- **Key:** `GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt`
- **Generator:** `grub-mkconfig` reads this and embeds into `/boot/grub/grub.cfg`

### 3. Runtime Boundary
- **Consumer:** GRUB2 bootloader (stage 2)
- **Inputs:** `theme.txt` + all referenced PNG assets
- **Output:** Rendered boot menu on firmware framebuffer
- **Failure modes:** Missing assets → render artifacts; parse errors → fallback to default theme

## Dependency Direction

```
GRUB2 (upstream) → consumes → Nuci Theme Package
grub-mkconfig → reads → /etc/default/grub → points to → Nuci Theme Package
Distribution Packaging → installs → Nuci Theme Package → /usr/share/grub/themes/
```

The theme package has **zero external dependencies**. It declares no build-time, runtime, or development dependencies.

## Constraints & Invariants

| Invariant | Enforcement |
|-----------|-------------|
| Every asset referenced in `theme.txt` exists in `Nuci/` | Manual verification; `grep` check |
| No external asset references | `theme.txt` uses only relative/glob paths |
| PNG format for all raster assets | GRUB2 requirement; no conversion pipeline |
| Single directory deployment | FHS + GRUB2 expectation |
| Installation path stability | `/usr/share/grub/themes/Nuci/` hardcoded in config |

## Extension Points

The theme package supports extension only through:
1. **Adding new icons** to `icons/` — requires corresponding boot entry class in GRUB2 configuration
2. **Replacing assets** — must preserve dimensions and naming pattern
3. **Modifying `theme.txt`** — must maintain GRUB2 theme specification compliance

No plugin system, no runtime configuration, no theming API.