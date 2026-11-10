# Rendering Pipeline — GRUB2 Theme Nuci

Details the rendering process from theme load to frame output.

## Pipeline Stages

```
Theme Load → Asset Decode → Layout Calculation → Widget Render → Frame Composition → Video Output
```

## Stage 1: Theme Load

**Input:** `theme.txt` file path from `GRUB_THEME`

**Process:**
1. Open and read theme.txt
2. Parse global properties (key: value)
3. Parse blocks (+ name { ... })
4. Validate required properties present
5. Build internal theme object model

**Output:** Parsed theme structure in memory

**Errors:** Silent fallback to default theme

## Stage 2: Asset Decode

**Input:** Asset paths from parsed theme

**Process:**
For each referenced asset:
1. Resolve path relative to theme.txt directory
2. Open file
3. Decode PNG (libpng via GRUB2's png module)
4. Convert to internal pixel format (ARGB8888)
5. Store in video memory / system memory

**Assets Decoded:**
- `background.png` → Full-screen texture
- `select_*.png` (8) → Sprite atlas or individual textures
- `terminal_box_*.png` (9) → Sprite atlas or individual textures
- `icons/*.png` → Decoded on-demand when menu populated

**Output:** Decoded pixel buffers in memory

**Errors:** Asset marked as failed; widget renders without it

## Stage 3: Layout Calculation

**Input:** Parsed theme properties, screen resolution

**Process:**
1. Get active video mode resolution (width × height)
2. Calculate absolute positions from percentages:
   - `left_px = (left% / 100) × screen_width`
   - `top_px = (top% / 100) × screen_height`
   - `width_px = (width% / 100) × screen_width`
   - `height_px = (height% / 100) × screen_height`
3. Calculate boot_menu item positions:
   - Item Y = `top_px + item_index × (item_height + item_spacing)`
   - Item X = `left_px + item_padding`
4. Calculate label position
5. Calculate terminal box position (when activated)

**Output:** Absolute pixel coordinates for all widgets

**Note:** Recalculated on video mode change

## Stage 4: Widget Render

### Background Widget
1. Scale `background.png` to screen resolution (bilinear)
2. Draw to framebuffer at (0, 0) full screen

### Boot Menu Widget
1. For each visible item:
   a. Determine if selected
   b. Render selection highlight (if selected):
      - Compose 8 sprites around item bounds
      - `select_n`/`select_s`: horizontal stretch across width
      - `select_e`/`select_w`: vertical stretch across height
      - Corners: fixed at corners
      - `select_c`: horizontal fill for top/bottom edges
   c. Render icon (if available):
      - Decode `icons/{class}.png` if not cached
      - Scale to `icon_width` × `icon_height` (or auto)
      - Draw at item left + `icon_spacing`
   d. Render text:
      - Use `item_font` (or global `font`)
      - Colour: `item_color` or `item_color_highlight`
      - Position: after icon + `icon_spacing`
2. Clip to menu bounds

### Label Widget
1. Format text (substitute `%d` for timeout)
2. Measure text dimensions with `font`
3. Position per `align` within label bounds
4. Render text with `color`

### Terminal Box Widget (On Demand)
1. When command line/edit mode activated:
2. Calculate box bounds from text area + padding
3. Compose 9-slice frame:
   - Corners: fixed at 4 corners
   - Edges: tile horizontally/vertically
   - Center: tile both directions
4. Render text input area inside frame

## Stage 5: Frame Composition

**Order (back to front):**
1. Background
2. Boot menu (with highlights)
3. Labels (timeout, etc.)
4. Terminal box (if active)
5. Cursor (if in command line/edit)

**Blending:** Alpha blending for transparent assets (sprites, icons)

**Double Buffering:** GRUB2 uses double-buffered framebuffer; compose to back buffer, swap on VSync

## Stage 6: Video Output

**BIOS:** VBE framebuffer write
**UEFI:** GOP framebuffer write

**Synchronisation:** VSync wait before buffer swap (tear-free)

## Resolution Handling

**Video Mode Sources:**
1. `GRUB_GFXMODE` in `/etc/default/grub` (e.g., `1920x1080`, `auto`)
2. Firmware default (UEFI GOP preferred mode)
3. Fallback: 640×480 (BIOS) or 1024×768 (UEFI)

**Theme Scaling:**
- Background: Scale to fill (aspect preserved or stretched per GRUB2)
- Menu: Percentage-based → absolute pixels
- Sprites: Pixel-perfect (no scaling) or 9-slice (terminal box)
- Icons: Scale to fit `icon_width`/`icon_height`
- Fonts: Render at specified point size (GRUB2 handles DPI)

**Aspect Ratio Changes:**
- Menu percentages adapt to new resolution
- Background re-scaled
- Sprites unchanged (pixel-perfect)
- Re-layout triggered on mode change

## Colour Handling

**Format:** ARGB8888 internal
**Theme Colours:** `#RRGGBB` → `0xFFRRGGBB` (opaque)
**Sprite Alpha:** From PNG alpha channel
**Blending:** Standard alpha over

**No Colour Management:** sRGB assumed; no ICC profiles, no gamma correction

## Font Rendering

**Engine:** GRUB2 built-in font renderer (FreeType not used)
**Font Source:**
- Firmware font (UEFI)
- `grub-mkfont` generated `.pf2` (BIOS)
- Theme-specified font must be available

**Rendering:** Glyph bitmap → alpha blend → framebuffer
**Kerning:** Basic (monospace-like for Unifont)
**Hinting:** None (bitmap fonts)

## Performance Optimisations

| Optimisation | Description |
|--------------|-------------|
| Asset caching | Decoded PNGs kept in memory |
| Sprite atlases | Selection/terminal sprites may be atlased |
| Dirty rects | Only changed regions redrawn (navigation) |
| Double buffering | Tear-free output |
| Lazy icon decode | Icons decoded on first menu population |

## Memory Usage

| Component | Estimate |
|-----------|----------|
| Background (1920×1080×4) | ~8 MB |
| Selection sprites (8 × 32×W×4) | ~0.5 MB |
| Terminal box sprites (9 × small) | ~0.1 MB |
| Icons (11 × 128×128×4) | ~0.7 MB |
| Font glyphs | ~0.5 MB |
| Framebuffers (2 × 1920×1080×4) | ~16 MB |
| **Total** | **~26 MB** |

## Related Documents

- [boot-sequence.md](./boot-sequence.md) — When rendering occurs
- [input-handling.md](./input-handling.md) — Interaction triggering re-render
- [asset-specifications.md](../api-reference/asset-specifications.md) — Asset format requirements
- [troubleshooting.md](../troubleshooting.md) — Rendering issues