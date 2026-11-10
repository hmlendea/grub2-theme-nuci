# Error Handling — GRUB2 Theme Nuci

Documents error handling patterns for the theme package and its integration with GRUB2.

## Theme-Level Error Handling

**No runtime error handling.** The theme is a static asset package with no executable code. Errors occur at GRUB2 theme engine level.

## GRUB2 Theme Engine Error Handling

### Parse Errors

| Error | Behaviour | User Visible |
|-------|-----------|--------------|
| Syntax error in theme.txt | Fallback to default theme | No (silent) |
| Unknown property | Ignored (forward compat) | No |
| Unknown block | Ignored | No |
| Missing required property | Fallback to default theme | No |
| Invalid value format | Undefined (may fallback) | No |

### Asset Load Errors

| Error | Behaviour | User Visible |
|-------|-----------|--------------|
| File not found | Widget renders without asset | Partial (missing graphic) |
| PNG decode failure | Widget renders without asset | Partial |
| Invalid dimensions | Undefined rendering | Visual artifact |
| Out of memory | GRUB2 may crash | System failure |

### Render Errors

| Error | Behaviour |
|-------|-----------|
| Video mode unsupported | Fallback to text mode |
| Framebuffer unavailable | Boot fails |
| Font unavailable | Built-in fallback font |

## Error Detection (Pre-Boot)

### Asset Integrity Verification

```bash
#!/bin/bash
# verify-assets.sh

set -e

echo "=== Asset Integrity Check ==="

# Theme descriptor
test -f Nuci/theme.txt || { echo "MISSING: theme.txt"; exit 1; }

# Background
test -f Nuci/background.png || { echo "MISSING: background.png"; exit 1; }
file Nuci/background.png | grep -q 'PNG image data' || { echo "INVALID: background.png"; exit 1; }

# Selection sprites (8)
count=$(ls Nuci/select_*.png 2>/dev/null | wc -l)
[ $count -eq 8 ] || { echo "SELECT SPRITES: $count/8"; exit 1; }
for f in Nuci/select_*.png; do
  file "$f" | grep -q 'PNG image data' || { echo "INVALID: $f"; exit 1; }
done

# Terminal box sprites (9)
count=$(ls Nuci/terminal_box_*.png 2>/dev/null | wc -l)
[ $count -eq 9 ] || { echo "TERMINAL SPRITES: $count/9"; exit 1; }
for f in Nuci/terminal_box_*.png; do
  file "$f" | grep -q 'PNG image data' || { echo "INVALID: $f"; exit 1; }
done

# Icons
test -d Nuci/icons && [ "$(ls -A Nuci/icons)" ] || { echo "MISSING: icons/"; exit 1; }
for f in Nuci/icons/*.png; do
  file "$f" | grep -q 'PNG image data' || { echo "INVALID: $f"; exit 1; }
done

# Reference integrity
missing=0
for ref in $(grep -oP '(?<=: "|= ")[^"]+\.png' Nuci/theme.txt | sed 's/\*//g' | sort -u); do
  ls Nuci/$ref 1>/dev/null 2>&1 || { echo "UNRESOLVED: $ref"; missing=1; }
done
[ $missing -eq 0 ] || exit 1

echo "=== All Checks Passed ==="
```

### Contract Verification

See [compatibility-contracts.md](../compatibility-contracts.md#automated-checks-recommended-for-ci) for full contract verification script.

## Boot-Time Error Diagnosis

### GRUB2 Command Line Debugging

At GRUB2 menu, press `c` for command line:

```bash
# Check theme path
echo $GRUB_THEME

# Check video mode
videomode

# List theme directory
ls /usr/share/grub/themes/Nuci/

# Test PNG decode (if supported)
# No direct command; observe rendering
```

### Common Failure Modes

| Symptom | Diagnosis Command | Resolution |
|---------|-------------------|------------|
| No theme | `grep GRUB_THEME /boot/grub/grub.cfg` | Set GRUB_THEME, run grub-mkconfig |
| Black background | `ls /usr/share/grub/themes/Nuci/background.png` | Restore background.png |
| No selection highlight | `ls /usr/share/grub/themes/Nuci/select_*.png` | Restore all 8 sprites |
| No icons | `ls /usr/share/grub/themes/Nuci/icons/` | Add missing icons |
| Text garbled | Check font availability | Ensure Unifont present |

## Error Reporting

### For Users

1. Run verification script
2. Collect diagnostics:
   ```bash
   grub2-install --version
   cat /etc/default/grub
   ls -la /usr/share/grub/themes/Nuci/
   ```
3. Report via GitHub Issues with:
   - Distribution and version
   - GRUB2 version
   - Firmware mode (BIOS/UEFI)
   - Screen resolution
   - Photo of boot screen

### For Maintainers

- Reproduce in VM (QEMU/OVMF for UEFI, QEMU for BIOS)
- Test with target GRUB2 version
- Check GRUB2 changelog for regressions
- Bisect if regression introduced

## Graceful Degradation

The theme degrades gracefully:

| Missing Asset | Degraded Behaviour |
|---------------|-------------------|
| background.png | Solid colour background |
| select_*.png (some) | Partial highlight |
| terminal_box_*.png (some) | Partial/no frame |
| icons/*.png | Fallback to linux/unknown |
| font | Built-in fallback |

## No Recovery Mechanism

Once GRUB2 falls back to default theme, no automatic recovery. User must:
1. Fix theme assets/configuration
2. Re-run `grub-mkconfig`
3. Reboot

## Related Documents

- [troubleshooting.md](../troubleshooting.md) — Detailed issue resolution
- [compatibility-contracts.md](../compatibility-contracts.md) — Contracts that prevent errors
- [testing.md](../testing.md) — Verification procedures
- [boot-sequence.md](../behaviour/boot-sequence.md) — When errors occur