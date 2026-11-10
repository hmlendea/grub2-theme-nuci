# Asset Load Flow — GRUB2 Theme Nuci

Details the asset loading process during theme initialisation.

## Flow Overview

```
Theme Parse → Asset Discovery → Parallel Decode → Validation → Cache → Ready
```

## Step-by-Step

### 1. Asset Discovery

**Trigger:** Theme parser encounters asset references

**Sources:**
- Global properties: `background`, `terminal_box`, `select`
- Block properties: (none directly reference assets)
- Icon discovery: Dynamic at menu population

**Resolution:**
- All paths relative to `theme.txt` directory
- Globs expanded by GRUB2 (`select_*.png` → 8 files)
- Icons: `icons/{class}.png` per boot entry

### 2. Background Load

**Priority:** Highest (first visible element)

**Process:**
```
1. Open background.png
2. Verify PNG signature
3. Decode headers (IHDR): dimensions, colour type, bit depth
4. Allocate pixel buffer (screen_width × screen_height × 4 bytes)
5. Decode IDAT chunks → pixel data
6. Scale to screen resolution (bilinear)
7. Store in background texture slot
```

**Failure:** Black background; continue

### 3. Selection Sprite Load

**Priority:** High (needed for initial selection highlight)

**Process:**
```
For each of 8 sprites (c, n, ne, e, se, s, sw, w):
  1. Open select_{dir}.png
  2. Decode PNG
  3. Verify dimensions compatible with item_height
  4. Store in sprite array[dir]
```

**Glob Expansion:** GRUB2 expands `select_*.png` at load time

**Failure:** Missing sprites → no highlight for that edge; continue

### 4. Terminal Box Sprite Load

**Priority:** Medium (only needed on `c`/`e` key)

**Process:**
```
For each of 9 sprites (c, n, ne, e, se, s, sw, w, nw):
  1. Open terminal_box_{dir}.png
  2. Decode PNG
  3. Verify 9-slice compatibility (corner/edge alignment)
  4. Store in terminal_sprite_array[dir]
```

**Glob Expansion:** GRUB2 expands `terminal_box_*.png` at load time

**Failure:** Command line works without frame; continue

### 5. Icon Load (Lazy)

**Priority:** Low (on-demand)

**Trigger:** Menu population iterates boot entries

**Process:**
```
For each boot entry:
  1. Extract class from --class field
  2. Check icon cache for class
  3. If not cached:
     a. Open icons/{class}.png
     b. Decode PNG
     c. Scale to icon_width × icon_height
     d. Store in icon cache
  4. Associate with menu item
```

**Fallback Chain:**
1. `icons/{class}.png` (exact match)
2. `icons/linux.png` (if entry has `--class linux`)
3. `icons/unknown.png` (final fallback)
4. No icon (if all missing)

### 6. Font Load

**Note:** Fonts not loaded as theme assets. GRUB2 uses:
- Firmware font (UEFI)
- `.pf2` font from `grub-mkfont` (BIOS)
- Theme `font` property must match available font

## Parallelisation

GRUB2 loads assets sequentially in practice, but order is:
1. Background (largest, first visible)
2. Selection sprites (needed immediately)
3. Terminal box sprites (deferred until needed)
4. Icons (lazy, on-demand)

## Memory Management

**Allocation:** GRUB2 uses internal memory allocator (grub_malloc)

**Lifetime:** Assets remain in memory until GRUB2 exits (boot)

**No Unloading:** Assets not freed; memory reclaimed by kernel

## Error Handling

| Asset | Failure Mode | User Impact |
|-------|--------------|-------------|
| Background | Decode error / missing | Black screen behind menu |
| Selection sprite | Decode error / missing | Partial/no highlight |
| Terminal box sprite | Decode error / missing | Command line without frame |
| Icon | Decode error / missing | Fallback icon or none |
| Font | Unavailable | Built-in fallback font |

**No User Notification:** All failures silent; fallback behaviour only.

## Verification Points

**At Load Time:**
- PNG signature valid
- Dimensions reasonable
- Colour type supported (RGB/RGBA)
- No CRC errors in chunks

**At Render Time:**
- Sprite alignment (selection, terminal box)
- Icon aspect ratio preserved
- Background covers screen

## Related Documents

- [theme-load.md](./theme-load.md) — Overall theme load sequence
- [asset-specifications.md](../api-reference/asset-specifications.md) — Asset requirements
- [rendering.md](../behaviour/rendering.md) — How assets used in rendering
- [troubleshooting.md](../troubleshooting.md) — Asset-related issues