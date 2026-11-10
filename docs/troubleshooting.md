# Troubleshooting — GRUB2 Theme Nuci

Common issues, diagnostics, and resolutions.

## Quick Diagnosis

| Symptom | Likely Cause | First Check |
|---------|--------------|-------------|
| Theme not applied | `GRUB_THEME` not set or wrong path | `grep GRUB_THEME /etc/default/grub` |
| Theme not applied | `grub-mkconfig` not run after change | Run `sudo grub-mkconfig -o /boot/grub/grub.cfg` |
| Theme not applied | Theme files not installed | `ls /usr/share/grub/themes/Nuci/` |
| Black/blank background | `background.png` missing or corrupt | `file /usr/share/grub/themes/Nuci/background.png` |
| Missing selection highlight | `select_*.png` sprites missing | `ls /usr/share/grub/themes/Nuci/select_*.png` |
| Terminal box broken | `terminal_box_*.png` sprites missing | `ls /usr/share/grub/themes/Nuci/terminal_box_*.png` |
| Icons not showing | Icon filename ≠ boot entry `class` | Check `class` in `grub.cfg` |
| Font not rendering | Unifont not available to GRUB2 | Check firmware font support |
| Layout misaligned | Resolution mismatch | Test at native resolution |
| Permission denied | Files not world-readable | `chmod 644` on files, `755` on dirs |

## Detailed Issues

### 1. Theme Not Applied at All

**Symptoms:** Default GRUB2 appearance (no background, standard menu).

**Diagnosis:**
```bash
# Check GRUB_THEME setting
grep GRUB_THEME /etc/default/grub

# Check generated config
grep -A2 -B2 'theme' /boot/grub/grub.cfg

# Check theme files exist
ls -la /usr/share/grub/themes/Nuci/
```

**Resolutions:**
- Set `GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt` in `/etc/default/grub`
- Run `sudo grub-mkconfig -o /boot/grub/grub.cfg`
- Verify theme directory exists and is readable
- Ensure no conflicting `GRUB_THEME` in `/etc/grub.d/` scripts

### 2. Background Not Displaying

**Symptoms:** Menu appears but background is black or solid colour.

**Diagnosis:**
```bash
# Verify background file
file /usr/share/grub/themes/Nuci/background.png

# Check theme.txt reference
grep background /usr/share/grub/themes/Nuci/theme.txt

# Check PNG validity
identify /usr/share/grub/themes/Nuci/background.png  # ImageMagick
```

**Resolutions:**
- Ensure `background.png` is valid PNG (not JPEG renamed)
- Verify `background: "background.png"` in `theme.txt`
- Check dimensions: GRUB2 scales but extreme sizes may fail
- Verify file permissions: `chmod 644 background.png`

### 3. Selection Highlight Missing or Corrupt

**Symptoms:** No visual feedback when navigating menu; or glitched graphics.

**Diagnosis:**
```bash
# Count selection sprites
ls /usr/share/grub/themes/Nuci/select_*.png | wc -l  # Must be 8

# List them
ls /usr/share/grub/themes/Nuci/select_*.png

# Check theme.txt glob
grep select /usr/share/grub/themes/Nuci/theme.txt
```

**Resolutions:**
- All 8 sprites required: `c`, `n`, `ne`, `e`, `se`, `s`, `sw`, `w`
- Verify `select_*.png` glob in `theme.txt` (not individual files)
- Check sprite dimensions match `item_height` (32px)
- Ensure PNGs are valid and not corrupted

### 4. Terminal Box (Command Line) Frame Broken

**Symptoms:** Pressing `c` shows command line but frame has gaps or missing pieces.

**Diagnosis:**
```bash
# Count terminal box sprites
ls /usr/share/grub/themes/Nuci/terminal_box_*.png | wc -l  # Must be 9

# List them
ls /usr/share/grub/themes/Nuci/terminal_box_*.png

# Check theme.txt glob
grep terminal_box /usr/share/grub/themes/Nuci/theme.txt
```

**Resolutions:**
- All 9 sprites required: `c`, `n`, `ne`, `e`, `se`, `s`, `sw`, `w`, `nw`
- Verify `terminal_box_*.png` glob in `theme.txt`
- 9-slice alignment: corners/edges must match for seamless scaling
- Check sprite dimensions are consistent

### 5. Icons Not Displaying

**Symptoms:** Generic placeholder or no icon next to boot entries.

**Diagnosis:**
```bash
# Check icons directory
ls /usr/share/grub/themes/Nuci/icons/

# Check boot entry classes in grub.cfg
grep -oP 'class\s+--class\s+\K\w+' /boot/grub/grub.cfg | sort -u

# Compare: icon filename (without .png) must match class exactly
```

**Resolutions:**
- Icon filename must match `class` field exactly (case-sensitive)
- Example: `class --class archlinux` → `icons/archlinux.png`
- Add missing icons to `icons/` directory
- No `theme.txt` change needed; GRUB2 auto-discovers
- Fallback: `icons/linux.png` for unknown Linux, `icons/unknown.png` for others

### 6. Font Not Rendering / Garbled Text

**Symptoms:** Menu text shows boxes, wrong characters, or missing glyphs.

**Diagnosis:**
```bash
# Check font specification in theme.txt
grep font /usr/share/grub/themes/Nuci/theme.txt

# Verify Unifont available to GRUB2
# GRUB2 uses firmware fonts or embedded fonts
```

**Resolutions:**
- Theme specifies: `font: "Unifont Regular 16"`
- Unifont must be available in firmware or GRUB2 font directory
- On UEFI: firmware usually provides Unifont
- On BIOS: may need `grub-mkfont` to create `.pf2` font file
- Alternative: specify available font family

### 7. Layout Misaligned / Off-Screen

**Symptoms:** Menu cut off, elements overlapping, wrong positions.

**Diagnosis:**
```bash
# Check theme.txt layout values (percentages)
grep -E '(left|top|width|height):' /usr/share/grub/themes/Nuci/theme.txt

# Check screen resolution at boot
# GRUB2 resolution set by GRUB_GFXMODE in /etc/default/grub
```

**Resolutions:**
- All positions/sizes in `theme.txt` are percentages (0-100%)
- Verify `GRUB_GFXMODE` matches display native resolution
- Common: `GRUB_GFXMODE=1920x1080` or `auto`
- Test at multiple resolutions
- Percentage values outside 0-100% have undefined behaviour

### 8. Permission Errors

**Symptoms:** Theme partially loads; some assets missing; "permission denied" in logs.

**Diagnosis:**
```bash
# Check permissions
find /usr/share/grub/themes/Nuci -type d ! -perm 755
find /usr/share/grub/themes/Nuci -type f ! -perm 644
```

**Resolutions:**
```bash
sudo find /usr/share/grub/themes/Nuci -type d -exec chmod 755 {} \;
sudo find /usr/share/grub/themes/Nuci -type f -exec chmod 644 {} \;
```

### 9. UEFI vs BIOS Differences

**Symptoms:** Theme works in one firmware mode but not the other.

**Diagnosis:**
- Check `GRUB_GFXMODE` and `GRUB_GFXPAYLOAD_LINUX` in `/etc/default/grub`
- UEFI: GRUB2 uses GOP (Graphics Output Protocol)
- BIOS: GRUB2 uses VBE (VESA BIOS Extensions)

**Resolutions:**
- Set `GRUB_GFXMODE=auto` for automatic detection
- Set `GRUB_GFXPAYLOAD_LINUX=keep` to preserve resolution
- Test both modes separately
- Some firmware implementations have limited resolution support

### 10. GRUB2 Version Incompatibility

**Symptoms:** Theme worked on older GRUB2 but breaks after update.

**Diagnosis:**
```bash
grub2-install --version
# or
grub-install --version
```

**Resolutions:**
- Check GRUB2 changelog for theme engine changes
- Test theme on target GRUB2 version before deploying
- Report upstream if GRUB2 introduces regression
- Pin GRUB2 version in package if necessary

## Diagnostic Commands Reference

```bash
# Full theme installation verification
verify-theme() {
  echo "=== Theme Installation Verification ==="
  echo "Theme directory:"
  ls -la /usr/share/grub/themes/Nuci/
  echo
  echo "GRUB_THEME setting:"
  grep GRUB_THEME /etc/default/grub
  echo
  echo "Generated config theme reference:"
  grep -i theme /boot/grub/grub.cfg | head -5
  echo
  echo "Asset counts:"
  echo "  Selection: $(ls /usr/share/grub/themes/Nuci/select_*.png 2>/dev/null | wc -l)/8"
  echo "  Terminal:  $(ls /usr/share/grub/themes/Nuci/terminal_box_*.png 2>/dev/null | wc -l)/9"
  echo "  Icons:     $(ls /usr/share/grub/themes/Nuci/icons/*.png 2>/dev/null | wc -l)"
  echo
  echo "Permissions:"
  find /usr/share/grub/themes/Nuci -type d ! -perm 755 -o -type f ! -perm 644 | wc -l
  echo "  (0 = OK)"
}

# Run verification
verify-theme
```

## Getting Help

If issue persists:

1. **Collect diagnostics:**
   ```bash
   verify-theme > theme-diagnostics.txt 2>&1
   grub2-install --version >> theme-diagnostics.txt
   cat /etc/default/grub >> theme-diagnostics.txt
   ```

2. **Check existing issues:** GitHub Issues for this repository

3. **Open new issue** with:
   - Distribution and version
   - GRUB2 version (`grub2-install --version`)
   - Firmware mode (BIOS/UEFI)
   - Screen resolution
   - Full `theme-diagnostics.txt` output
   - Photo of boot screen (if visual issue)

4. **Reference:** [SECURITY.md](../SECURITY.md) for vulnerability reporting