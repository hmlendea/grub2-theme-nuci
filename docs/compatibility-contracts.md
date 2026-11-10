# Compatibility Contracts — GRUB2 Theme Nuci

This document defines the compatibility contracts that must remain stable for correct operation across versions.

## Contract Catalogue

| Contract | Owner | Invariant | Verification | Change Policy |
|----------|-------|-----------|--------------|---------------|
| `theme.txt` format | GRUB2 Upstream | Keys, blocks, value syntax per GRUB2 theme spec | GRUB2 parses without error | Backward-compatible only; test with target GRUB2 version |
| Asset filenames in `theme.txt` | Maintainer | Every referenced file exists in `Nuci/` | `grep` asset paths; `test -f` each | Additions allowed; removals require `theme.txt` update |
| PNG format and dimensions | GRUB2 Upstream | Valid PNG; dimensions suitable for target resolutions | `file` command; visual boot test | Changes require testing on target hardware/resolutions |
| Installation path `/usr/share/grub/themes/Nuci/` | Distribution / FHS | Stable absolute path | Packaging scripts | Relocation requires `GRUB_THEME` update and `grub-mkconfig` re-run |
| `GRUB_THEME` key in `/etc/default/grub` | System Administrator | Key exists with correct absolute path | `grep GRUB_THEME /etc/default/grub` | Must not be removed or changed without re-running `grub-mkconfig` |
| Sprite pattern `select_*.png` | Maintainer | Resolves to exactly 8 directional sprites | `ls select_*.png \| wc -l` = 8 | Pattern fixed by GRUB2; do not change |
| Terminal box pattern `terminal_box_*.png` | Maintainer | Resolves to exactly 9 9-slice sprites | `ls terminal_box_*.png \| wc -l` = 9 | Pattern fixed by GRUB2; do not change |
| Icon filename ↔ boot entry class mapping | GRUB2 Config | Icon filename matches `class` field exactly | Boot menu shows icon | New icons: add file; no `theme.txt` change |
| Font specification `Unifont Regular 16` | GRUB2 / Distribution | Font available to GRUB2 at boot | Boot test; glyph rendering | Change requires font availability verification |
| Colour format `#RRGGBB` | GRUB2 Upstream | 24-bit hex RGB only | Visual verification | No alpha; format fixed by spec |
| Percentage layout (0-100%) | GRUB2 Upstream | Positions/dimensions within screen bounds | Visual verification | Values outside 0-100% undefined |
| GPL-3.0-or-later licence | Maintainer | All assets under compatible licence | Manual audit | New assets must be GPL-3.0-or-later compatible |

## Detailed Contract Specifications

### 1. Theme Descriptor Format Contract

**Owner:** GRUB2 Upstream
**Scope:** `Nuci/theme.txt` syntax and semantics

**Invariants:**
- Global properties: `key: value` format (colon + space)
- Blocks: `+ name {` ... `}` with `key = value` properties (equals + spaces)
- Comments: `#` at line start
- String values: double-quoted, UTF-8
- Colour values: `#RRGGBB` (6 hex digits)
- Font values: `"Family Style Size"` (three space-separated tokens)
- Percentage values: `N%` (integer + percent sign)
- Numeric values: integer (pixels)

**Verification:**
```bash
# Syntax check - GRUB2 will fail to parse on error
# No formal validator exists; boot test is authoritative
```

**Change Policy:**
- **Additive:** New properties/blocks allowed (GRUB2 ignores unknown)
- **Modifying:** Existing property values may change (visual effect)
- **Removing:** Properties may be removed (reverts to GRUB2 default)
- **Breaking:** Syntax changes that cause parse failure prohibited

### 2. Asset Reference Contract

**Owner:** Maintainer
**Scope:** All asset paths in `theme.txt`

**Invariants:**
- All paths relative to `theme.txt` location
- No absolute paths (`/...`)
- No parent directory references (`../`)
- No URLs (`http://`, `https://`, `file://`)
- Glob patterns (`*`, `?`) only for declared sprite collections

**Verification:**
```bash
# Check for violations
grep -E '(^|: )(/|\.\.|https?://)' Nuci/theme.txt && echo "VIOLATION FOUND"

# Verify all references resolve
for ref in $(grep -oP '(?<=: "|= ")[^"]+\.png' Nuci/theme.txt | sed 's/\*//g' | sort -u); do
  # Expand globs manually for verification
  ls Nuci/$ref 2>/dev/null || echo "MISSING: $ref"
done
```

**Change Policy:**
- **Adding assets:** Add file to `Nuci/`; add reference to `theme.txt`
- **Removing assets:** Remove reference from `theme.txt` first; then delete file
- **Renaming assets:** Update `theme.txt` reference; rename file atomically
- **Moving assets:** Not supported (single directory only)

### 3. Sprite Collection Contracts

#### Selection Sprites (`select_*.png`)

**Owner:** Maintainer
**Invariant:** Exactly 8 files matching pattern: `c`, `n`, `ne`, `e`, `se`, `s`, `sw`, `w`

**Verification:**
```bash
ls Nuci/select_*.png | wc -l  # Must equal 8
for d in c n ne e se s sw w; do test -f "Nuci/select_${d}.png" || echo "MISSING: $d"; done
```

**Change Policy:**
- **All 8 must be updated together** for visual consistency
- **Pattern cannot change** — GRUB2 expects `select_*.png` with these 8 suffixes
- **Dimensions** should match `item_height` (32px) and menu item width

#### Terminal Box Sprites (`terminal_box_*.png`)

**Owner:** Maintainer
**Invariant:** Exactly 9 files matching pattern: `c`, `n`, `ne`, `e`, `se`, `s`, `sw`, `w`, `nw`

**Verification:**
```bash
ls Nuci/terminal_box_*.png | wc -l  # Must equal 9
for d in c n ne e se s sw w nw; do test -f "Nuci/terminal_box_${d}.png" || echo "MISSING: $d"; done
```

**Change Policy:**
- **All 9 must be updated together** for visual consistency
- **Pattern cannot change** — GRUB2 expects `terminal_box_*.png` with these 9 suffixes
- **9-slice compatibility:** Corner/edge slices must align for seamless scaling

### 4. Icon Mapping Contract

**Owner:** GRUB2 Configuration (boot entry `class` field)
**Invariant:** Icon filename (without `.png`) matches boot entry `class` exactly

**Mapping:**
| Boot Entry Class | Icon File | Required |
|------------------|-----------|----------|
| `archlinux` | `icons/archlinux.png` | If entry exists |
| `debian` | `icons/debian.png` | If entry exists |
| `fedora` | `icons/fedora.png` | If entry exists |
| `ubuntu` | `icons/ubuntu.png` | If entry exists |
| `windows` | `icons/windows.png` | If entry exists |
| `uefi` | `icons/uefi.png` | If entry exists |
| `memtest` | `icons/memtest.png` | If entry exists |
| `shutdown` | `icons/shutdown.png` | If entry exists |
| `reboot` | `icons/reboot.png` | If entry exists |
| `linux` | `icons/linux.png` | Fallback |
| `unknown` | `icons/unknown.png` | Fallback |

**Verification:**
```bash
# Check boot entries have matching icons
# Requires parsing GRUB2 config or boot observation
```

**Change Policy:**
- **Adding icon:** Place `{class}.png` in `icons/`; no `theme.txt` change
- **Removing icon:** Delete file; GRUB2 falls back to generic
- **Renaming icon:** Must match `class` change in GRUB2 config

### 5. Installation Path Contract

**Owner:** Distribution / FHS
**Invariant:** Theme installed at `/usr/share/grub/themes/Nuci/`

**Verification:**
```bash
test -d /usr/share/grub/themes/Nuci && test -f /usr/share/grub/themes/Nuci/theme.txt
```

**Change Policy:**
- **Relocation prohibited** without coordinated change:
  1. Update package install path
  2. Update `GRUB_THEME` in `/etc/default/grub`
  3. Run `grub-mkconfig`
  4. Update documentation

### 6. GRUB2 Version Compatibility Contract

**Owner:** GRUB2 Upstream
**Scope:** Theme specification compatibility across GRUB2 versions

**Tested Versions:**
- GRUB 2.04 (baseline)
- GRUB 2.06 (common in current distributions)
- GRUB 2.12 (newer)

**Compatibility Strategy:**
- Use only stable theme specification features
- Avoid version-specific properties
- Test on minimum supported GRUB2 version

**Verification:**
```bash
# In target environment
grub2-install --version
# Boot test with target GRUB2 version
```

**Change Policy:**
- Monitor GRUB2 changelog for theme engine changes
- Update theme if upstream introduces breaking changes
- Document minimum GRUB2 version in README if required

### 7. Licence Compatibility Contract

**Owner:** Maintainer
**Invariant:** All assets licensed under GPL-3.0-or-later or compatible

**Verification:**
- Manual audit of asset sources
- `LICENSE` file at repository root
- No proprietary or incompatible-licence assets

**Change Policy:**
- New assets must have compatible licence
- Document asset provenance in commit messages
- Reject contributions with unclear licensing

## Contract Violation Detection

### Automated Checks (Recommended for CI)

```bash
#!/bin/bash
# verify-contracts.sh

set -e

echo "=== Verifying Theme Contracts ==="

# 1. Asset reference integrity
echo "Checking asset references..."
missing=0
for ref in $(grep -oP '(?<=: "|= ")[^"]+\.png' Nuci/theme.txt | sed 's/\*//g' | sort -u); do
  if ! ls Nuci/$ref 1>/dev/null 2>&1; then
    echo "  MISSING: $ref"
    missing=1
  fi
done
[ $missing -eq 0 ] && echo "  All references resolve"

# 2. Sprite collection completeness
echo "Checking sprite collections..."
select_count=$(ls Nuci/select_*.png 2>/dev/null | wc -l)
term_count=$(ls Nuci/terminal_box_*.png 2>/dev/null | wc -l)
[ $select_count -eq 8 ] && echo "  Selection sprites: 8/8 OK" || echo "  Selection sprites: $select_count/8 FAIL"
[ $term_count -eq 9 ] && echo "  Terminal box sprites: 9/9 OK" || echo "  Terminal box sprites: $term_count/9 FAIL"

# 3. No external references
echo "Checking for external references..."
if grep -qE '(^|: )(/|\.\.|https?://)' Nuci/theme.txt; then
  echo "  EXTERNAL REFERENCE VIOLATION"
  exit 1
else
  echo "  No external references OK"
fi

# 4. PNG validity
echo "Checking PNG validity..."
invalid=0
for f in Nuci/*.png Nuci/icons/*.png; do
  if ! file "$f" | grep -q 'PNG image data'; then
    echo "  INVALID PNG: $f"
    invalid=1
  fi
done
[ $invalid -eq 0 ] && echo "  All PNGs valid"

# 5. Permissions (if installed)
if [ -d /usr/share/grub/themes/Nuci ]; then
  echo "Checking installed permissions..."
  bad_perms=$(find /usr/share/grub/themes/Nuci -type d ! -perm 755 -o -type f ! -perm 644 | wc -l)
  [ $bad_perms -eq 0 ] && echo "  Permissions OK" || echo "  Permission violations: $bad_perms"
fi

echo "=== Contract Verification Complete ==="
```

### Manual Verification Checklist

| Check | Frequency | Method |
|-------|-----------|--------|
| Asset reference integrity | Every change | `verify-contracts.sh` |
| Sprite collection completeness | Every sprite change | Count files |
| PNG validity | Every asset change | `file` command |
| Boot visual verification | Every release | VM/hardware boot test |
| GRUB2 version compatibility | New GRUB2 release | Test on target version |
| Licence audit | New asset addition | Manual review |

## Versioning and Compatibility

**Theme Versioning:** No formal version scheme; repository tags indicate releases.

**Compatibility Guarantees:**
- **Patch-level:** Asset replacements preserving contracts → compatible
- **Minor-level:** New icons, colour tweaks, layout adjustments → compatible
- **Major-level:** `theme.txt` structure changes, sprite pattern changes → test required

**Deprecation Policy:** No deprecation; breaking changes require coordinated release with documentation update.