# Invariants — GRUB2 Theme Nuci

System-wide invariants and contracts that must remain true for correct operation.

## Asset Reference Integrity

**Invariant:** Every asset path referenced in `Nuci/theme.txt` resolves to an existing file in `Nuci/`.

**Verification:** `grep -oP '(?<=: ")[^"]+\.png' Nuci/theme.txt | sort -u | while read f; do test -f "Nuci/$f" || echo "MISSING: $f"; done`

**Failure mode:** GRUB2 fails to load asset → render artifact or fallback.

## Installation Path Stability

**Invariant:** The theme is installed at `/usr/share/grub/themes/Nuci/` and `GRUB_THEME` in `/etc/default/grub` points to `/usr/share/grub/themes/Nuci/theme.txt`.

**Verification:** `test -f /usr/share/grub/themes/Nuci/theme.txt && grep -q 'GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt' /etc/default/grub`

**Failure mode:** GRUB2 cannot find theme → falls back to default/text mode.

## GRUB2 Theme Specification Conformance

**Invariant:** `Nuci/theme.txt` syntax conforms to the GRUB2 theme specification for the target GRUB2 version.

**Verification:** `grub2-theme-preview` (if available) or boot test with target GRUB2 version.

**Failure mode:** Parse error → fallback to default theme; silent layout corruption.

## Asset Format Validity

**Invariant:** All raster assets are valid PNG files readable by GRUB2's PNG loader.

**Verification:** `file Nuci/*.png Nuci/icons/*.png | grep -v 'PNG image data'`

**Failure mode:** Corrupt/invalid PNG → render artifact or fallback.

## Sprite Pattern Completeness

**Invariant:** The `select_*.png` pattern resolves to exactly 8 directional sprites (c, n, ne, e, se, s, sw, w).

**Verification:** `ls Nuci/select_*.png | wc -l` → must equal 8.

**Failure mode:** Missing directions → selection highlight gaps.

## Terminal Box Pattern Completeness

**Invariant:** The `terminal_box_*.png` pattern resolves to exactly 9 9-slice sprites (c, n, ne, e, se, s, sw, w, nw).

**Verification:** `ls Nuci/terminal_box_*.png | wc -l` → must equal 9.

**Failure mode:** Missing slices → terminal box render gaps.

## No External Asset References

**Invariant:** `theme.txt` contains no absolute paths, parent directory references (`../`), or URLs.

**Verification:** `grep -E '(https?://|\.\./|^/)' Nuci/theme.txt` → must return empty.

**Failure mode:** GRUB2 cannot resolve path → asset not loaded.

## Single Theme Directory

**Invariant:** All theme assets reside within `Nuci/` with no subdirectories except `icons/`.

**Verification:** `find Nuci -type f | grep -v '^Nuci/icons/' | grep -v '^Nuci/[^/]*$'` → must return empty.

**Failure mode:** GRUB2 cannot find assets referenced by relative path.

## Permission Consistency

**Invariant:** Installed theme directories have 755 permissions; files have 644 permissions.

**Verification:** `find /usr/share/grub/themes/Nuci -type d ! -perm 755 -o -type f ! -perm 644` → must return empty.

**Failure mode:** GRUB2 cannot read assets (permission denied) → fallback.

## Licence Compatibility

**Invariant:** All assets are licensed under GPL-3.0-or-later or compatible licence.

**Verification:** Manual audit of asset sources; `LICENSE` file present at root.

**Failure mode:** Legal distribution risk; downstream packaging rejection.