# Asset Specifications — GRUB2 Theme Nuci

Technical requirements for all raster assets in the theme package.

## General PNG Requirements

| Requirement | Specification |
|-------------|---------------|
| Format | PNG (Portable Network Graphics) |
| Colour Type | Truecolour with alpha (RGBA) or Truecolour (RGB) |
| Bit Depth | 8-bit per channel (24-bit RGB or 32-bit RGBA) |
| Compression | DEFLATE (standard PNG) |
| Interlacing | Non-interlaced (progressive loading not needed) |
| Colour Space | sRGB (implicit) |
| Transparency | Alpha channel for sprites; optional for background |

## Asset Catalogue

### 1. Background (`background.png`)

| Property | Specification |
|----------|---------------|
| Filename | `background.png` |
| Count | 1 |
| Purpose | Full-screen background behind boot menu |
| Dimensions | Any; GRUB2 scales to screen resolution |
| Recommended | Match common display resolutions (1920×1080, 3840×2160) |
| Aspect Ratio | Preserve; GRUB2 scales proportionally |
| Transparency | Not used (opaque background) |
| File Size | < 500 KB recommended (boot time impact) |
| Reference | `background: "background.png"` in theme.txt |

**Design Notes:**
- Avoid critical content at edges (may be cropped on different aspect ratios)
- Centre-weighted composition preferred
- Test at 4:3, 16:9, 16:10, 21:9

### 2. Selection Sprites (`select_*.png`)

| Property | Specification |
|----------|---------------|
| Filenames | `select_c.png`, `select_n.png`, `select_ne.png`, `select_e.png`, `select_se.png`, `select_s.png`, `select_sw.png`, `select_w.png` |
| Count | 8 (exactly) |
| Purpose | 8-directional border for selected menu item highlight |
| Pattern | `select_*.png` glob in theme.txt |
| Dimensions | Height = `item_height` (32px); Width = variable |
| Transparency | Required (alpha channel for rounded corners, etc.) |
| Alignment | Pixel-perfect alignment between adjacent sprites |

**Sprite Mapping:**

```
select_nw  select_n  select_ne   (nw not used in select_*; only 8 sprites)
select_w   select_c  select_e
select_sw  select_s  select_se
```

**Note:** Selection widget uses 8 sprites (no center). The `c` sprite is the horizontal middle segment for top/bottom edges.

**Dimensions:**
- `select_n`, `select_s`: Width = menu item width; Height = border thickness
- `select_e`, `select_w`: Width = border thickness; Height = item_height
- `select_ne`, `select_nw`, `select_se`, `select_sw`: Square (border thickness × border thickness)
- `select_c`: Horizontal fill segment (1px wide × border thickness)

**Reference:** `select: "select_*.png"` in theme.txt

### 3. Terminal Box Sprites (`terminal_box_*.png`)

| Property | Specification |
|----------|---------------|
| Filenames | `terminal_box_c.png`, `terminal_box_n.png`, `terminal_box_ne.png`, `terminal_box_e.png`, `terminal_box_se.png`, `terminal_box_s.png`, `terminal_box_sw.png`, `terminal_box_w.png`, `terminal_box_nw.png` |
| Count | 9 (exactly) |
| Purpose | 9-slice scalable frame for GRUB2 command line / edit mode |
| Pattern | `terminal_box_*.png` glob in theme.txt |
| Dimensions | Variable; must align for seamless 9-slice scaling |
| Transparency | Required (alpha channel) |
| Alignment | Critical: edges must tile; corners must match edges |

**9-Slice Layout:**

```
terminal_box_nw  terminal_box_n  terminal_box_ne
terminal_box_w   terminal_box_c  terminal_box_e
terminal_box_sw  terminal_box_s  terminal_box_se
```

**Slice Roles:**
- **Corners** (`nw`, `ne`, `sw`, `se`): Fixed size, not scaled
- **Edges** (`n`, `s`): Horizontally tiled; `e`, `w`: Vertically tiled
- **Center** (`c`): Both directions tiled (fill)

**Dimension Constraints:**
- All corners same dimensions (e.g., 16×16)
- Horizontal edges (`n`, `s`): Height = corner height; Width = 1px (tile unit)
- Vertical edges (`e`, `w`): Width = corner width; Height = 1px (tile unit)
- Center (`c`): 1×1 or small tile

**Reference:** `terminal_box: "terminal_box_*.png"` in theme.txt

### 4. Icons (`icons/*.png`)

| Property | Specification |
|----------|---------------|
| Filenames | `{class}.png` (e.g., `archlinux.png`, `ubuntu.png`) |
| Count | Variable (currently 11) |
| Purpose | Distribution/OS icons in boot menu |
| Discovery | Automatic by GRUB2: matches boot entry `class` field |
| Dimensions | Square recommended (e.g., 64×64, 128×128) |
| Transparency | Required (alpha channel for non-rectangular logos) |
| Aspect Ratio | 1:1 (square) |
| File Size | < 50 KB each recommended |
| Reference | No theme.txt entry; auto-discovered |

**Current Inventory:**

| Filename | Class | Distribution |
|----------|-------|--------------|
| `archlinux.png` | `archlinux` | Arch Linux |
| `debian.png` | `debian` | Debian |
| `fedora.png` | `fedora` | Fedora |
| `ubuntu.png` | `ubuntu` | Ubuntu |
| `windows.png` | `windows` | Windows |
| `uefi.png` | `uefi` | UEFI Firmware |
| `memtest.png` | `memtest` | Memtest86+ |
| `shutdown.png` | `shutdown` | System Shutdown |
| `reboot.png` | `reboot` | System Reboot |
| `linux.png` | `linux` | Generic Linux (fallback) |
| `unknown.png` | `unknown` | Unknown/Other (fallback) |

**Adding New Icons:**
1. Determine boot entry `class` (from `grub.cfg` or distribution docs)
2. Create `{class}.png` with transparent background
3. Place in `Nuci/icons/`
4. No theme.txt modification needed

**Icon Design Guidelines:**
- Square canvas (e.g., 128×128)
- Centred logo with padding
- Transparent background
- Recognisable at small sizes (32×32 in menu)
- Consistent visual style across icons

## File Size Budget

| Asset Category | Target Max | Rationale |
|----------------|------------|-----------|
| Background | 500 KB | Largest asset; decoded at boot |
| Selection sprites (8) | 20 KB each | Small; decoded once |
| Terminal box sprites (9) | 10 KB each | Small; decoded once |
| Icons (per icon) | 50 KB | Multiple; decoded on demand |
| **Total theme** | **~2 MB** | Boot time impact |

## Validation Checklist

For each asset addition/modification:

- [ ] Valid PNG (`file` command shows "PNG image data")
- [ ] Correct dimensions for asset type
- [ ] Transparency where required (sprites, icons)
- [ ] No colour profile chunks (sRGB only)
- [ ] Filename matches naming convention exactly
- [ ] File permissions: 644
- [ ] Referenced correctly in theme.txt (if applicable)
- [ ] Visual verification at boot

## Tools

**Verify PNG:**
```bash
file asset.png
# Should output: "PNG image data, WIDTH x HEIGHT, 8-bit/color RGBA, non-interlaced"
```

**Check dimensions:**
```bash
identify asset.png  # ImageMagick
# or
pnginfo asset.png   # libpng
```

**Optimise PNG:**
```bash
oxipng -o max asset.png  # Lossless optimisation
# or
pngcrush -ow asset.png
```

**Batch verify all:**
```bash
for f in Nuci/*.png Nuci/icons/*.png; do
  if ! file "$f" | grep -q 'PNG image data'; then
    echo "INVALID: $f"
  fi
done
```