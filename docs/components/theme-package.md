# Theme Package Component — GRUB2 Theme Nuci

Deep dive on the Nuci theme package: descriptor, background, selection sprites, terminal box sprites, and icons.

## Component Overview

**Name:** Nuci Theme Package
**Location:** `Nuci/` (repository root) → deployed to `/usr/share/grub/themes/Nuci/`
**Type:** Static asset package (data only)
**Lifecycle:** Deployed once; updated by replacing entire directory
**Owner:** Repository maintainer

## Subcomponents

### 1. Theme Descriptor (`theme.txt`)

**Role:** Authoritative configuration source; parsed by GRUB2 at boot.

**Structure:**
- Global properties (background, colours, fonts, terminal box pattern)
- `+ boot_menu` block (menu layout, item styling, selection pattern)
- `+ label` block (timeout message layout and formatting)

**Key Responsibilities:**
- Declares all asset references (relative paths/globs)
- Defines visual layout (positions, dimensions, spacing)
- Specifies colour palette and font stack
- Defines selection and terminal box sprite patterns

**Dependencies:** None (self-contained)

**Failure Modes:**
- Parse error → GRUB2 falls back to default theme
- Missing referenced asset → render artifact
- Invalid colour/font format → undefined behaviour

**Testing:** Boot verification with target GRUB2 version.

---

### 2. Background Asset (`background.png`)

**Role:** Boot menu backdrop image.

**Specification:**
- **Format:** PNG
- **Dimensions:** No fixed requirement; GRUB2 scales to screen resolution
- **Recommended:** 1920×1080 (16:9) or 1024×768 (4:3) for common resolutions
- **Colour space:** sRGB
- **Transparency:** Not used (opaque background)

**Reference:** `desktop-image: "background.png"` (global property)

**Lifecycle:** Static; replaced only by maintainer commit.

**Testing:** Visual verification at boot across target resolutions.

---

### 3. Selection Sprites (`select_*.png`)

**Role:** 8-directional highlight rendered around the selected boot menu item.

**Files (8):**
| File | Direction | Purpose |
|------|-----------|---------|
| `select_c.png` | Centre | Default/fallback |
| `select_n.png` | North | Cursor moving up |
| `select_ne.png` | North-East | Diagonal up-right |
| `select_e.png` | East | Cursor moving right |
| `select_se.png` | South-East | Diagonal down-right |
| `select_s.png` | South | Cursor moving down |
| `select_sw.png` | South-West | Diagonal down-left |
| `select_w.png` | West | Cursor moving left |

**Specification:**
- **Format:** PNG with alpha transparency
- **Dimensions:** Must match `item_height` (32px) and menu item width
- **Pattern:** `selected_item_pixmap_style = "select_*.png"` in `boot_menu` block
- **Rendering:** GRUB2 selects sprite based on cursor movement direction

**Lifecycle:** Static; all 8 must be updated together for visual consistency.

**Testing:** Navigate menu with keyboard; verify highlight renders correctly in all directions.

---

### 4. Terminal Box Sprites (`terminal_box_*.png`)

**Role:** 9-slice scalable frame for the GRUB2 command-line terminal window.

**Files (9):**
| File | Slice | Position |
|------|-------|----------|
| `terminal_box_c.png` | Centre | Interior (tiled/stretched) |
| `terminal_box_n.png` | North | Top edge |
| `terminal_box_ne.png` | North-East | Top-right corner |
| `terminal_box_e.png` | East | Right edge |
| `terminal_box_se.png` | South-East | Bottom-right corner |
| `terminal_box_s.png` | South | Bottom edge |
| `terminal_box_sw.png` | South-West | Bottom-left corner |
| `terminal_box_w.png` | West | Left edge |
| `terminal_box_nw.png` | North-West | Top-left corner |

**Specification:**
- **Format:** PNG with alpha transparency
- **Dimensions:** Corner/edge slices typically small (e.g., 16×16); centre slice tileable
- **Pattern:** `terminal-box: "terminal_box_*.png"` (global property)
- **Rendering:** GRUB2 9-slice scaling to `terminal-width` × `terminal-height`

**Lifecycle:** Static; all 9 must be updated together for visual consistency.

**Testing:** Enter GRUB2 command line (`c` at boot menu); verify terminal frame renders without gaps.

---

### 5. Icons (`icons/*.png`)

**Role:** Class-specific icons displayed next to boot menu entries.

**Mechanism:** GRUB2 matches boot entry `class` field to icon filename (e.g., `class "archlinux"` → `icons/archlinux.png`).

**Specification:**
- **Format:** PNG
- **Dimensions:** Square recommended (e.g., 64×64, 128×128); GRUB2 scales
- **Transparency:** Supported (alpha channel)
- **Naming:** Lowercase, hyphenated class identifiers

**Common Classes:**
| Class | Icon File | Description |
|-------|-----------|-------------|
| `archlinux` | `archlinux.png` | Arch Linux |
| `debian` | `debian.png` | Debian |
| `fedora` | `fedora.png` | Fedora |
| `ubuntu` | `ubuntu.png` | Ubuntu |
| `linuxmint` | `linuxmint.png` | Linux Mint |
| `opensuse` | `opensuse.png` | openSUSE |
| `manjaro` | `manjaro.png` | Manjaro |
| `gentoo` | `gentoo.png` | Gentoo |
| `windows` | `windows.png` | Windows Boot Manager |
| `uefi` | `uefi.png` | UEFI Firmware Settings |
| `memtest` | `memtest.png` | Memory Test |
| `shutdown` | `shutdown.png` | System Shutdown |
| `reboot` | `reboot.png` | System Reboot |
| `linux` | `linux.png` | Generic Linux |
| `unknown` | `unknown.png` | Fallback for unrecognised classes |

**Lifecycle:** Extensible — new icons added by placing file in `icons/`; no `theme.txt` change needed.

**Testing:** Boot menu displays correct icon for each entry class.

---

## Internal Consistency Rules

1. **Asset completeness:** Every glob pattern in `theme.txt` must resolve to the expected number of files.
2. **Dimensional harmony:** Selection sprite height ≈ `item_height` (32px); terminal box slices compatible with 9-slice scaling.
3. **Colour coherence:** Sprite transparency works against `desktop-color` fallback.
4. **Font availability:** `Unifont Regular 16` must be loadable by GRUB2 (standard in most distributions).

## Modification Impact Matrix

| Change | Affected Files | Verification Required |
|--------|----------------|----------------------|
| Update background | `background.png` | Visual boot test |
| Change menu layout | `theme.txt` (boot_menu block) | Boot test; verify positions |
| Change colours | `theme.txt` (global + blocks) | Boot test; verify contrast |
| Change font | `theme.txt` (font properties) | Boot test; verify glyph rendering |
| Add selection sprite direction | `select_*.png` (new file) + `theme.txt` pattern | Not supported (GRUB2 expects 8) |
| Replace selection sprites | All 8 `select_*.png` | Navigate menu; verify all directions |
| Replace terminal box sprites | All 9 `terminal_box_*.png` | Enter command line; verify frame |
| Add icon | `icons/{class}.png` | Boot with entry of that class |
| Remove icon | Delete `icons/{class}.png` | Verify fallback icon used |

## Deployment Contract

- **Unit:** Entire `Nuci/` directory
- **Destination:** `/usr/share/grub/themes/Nuci/`
- **Permissions:** Directories 755, files 644
- **Atomicity:** Replace entire directory; no partial updates
- **Post-deploy:** `grub-mkconfig -o /boot/grub/grub.cfg` required