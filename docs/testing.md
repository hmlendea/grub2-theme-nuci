# Testing and Verification — GRUB2 Theme Nuci

This document describes the test strategy, verification procedures, coverage gaps, and constraints.

## Test Strategy

**No automated test suite exists.** The repository is a static asset package with no executable code. Verification is entirely manual and visual.

**Test Levels:**
1. **Asset Integrity Checks** — File existence, format validity, completeness
2. **Contract Verification** — Theme descriptor references, sprite counts, permissions
3. **Boot Visual Verification** — Full boot test on target hardware/VM
4. **Regression Verification** — Compare against known-good baseline

## Verification Procedures

### 1. Asset Integrity Checks (Pre-Commit)

```bash
#!/bin/bash
# verify-assets.sh

set -e

echo "=== Asset Integrity Verification ==="

# Theme descriptor exists
test -f Nuci/theme.txt && echo "theme.txt: OK" || { echo "theme.txt: MISSING"; exit 1; }

# Background exists
test -f Nuci/background.png && echo "background.png: OK" || { echo "background.png: MISSING"; exit 1; }

# Selection sprites (8)
select_count=$(ls Nuci/select_*.png 2>/dev/null | wc -l)
[ $select_count -eq 8 ] && echo "Selection sprites: $select_count/8 OK" || { echo "Selection sprites: $select_count/8 FAIL"; exit 1; }

# Terminal box sprites (9)
term_count=$(ls Nuci/terminal_box_*.png 2>/dev/null | wc -l)
[ $term_count -eq 9 ] && echo "Terminal box sprites: $term_count/9 OK" || { echo "Terminal box sprites: $term_count/9 FAIL"; exit 1; }

# Icons directory exists with content
test -d Nuci/icons && [ "$(ls -A Nuci/icons)" ] && echo "icons/: OK" || { echo "icons/: EMPTY OR MISSING"; exit 1; }

# All PNGs valid
invalid=0
for f in Nuci/*.png Nuci/icons/*.png; do
  if ! file "$f" | grep -q 'PNG image data'; then
    echo "INVALID PNG: $f"
    invalid=1
  fi
done
[ $invalid -eq 0 ] && echo "PNG validity: OK"

# No external references in theme.txt
if grep -qE '(^|: )(/|\.\.|https?://)' Nuci/theme.txt; then
  echo "EXTERNAL REFERENCE VIOLATION in theme.txt"
  exit 1
else
  echo "External references: NONE OK"
fi

# All theme.txt references resolve
missing=0
for ref in $(grep -oP '(?<=: "|= ")[^"]+\.png' Nuci/theme.txt | sed 's/\*//g' | sort -u); do
  if ! ls Nuci/$ref 1>/dev/null 2>&1; then
    echo "UNRESOLVED REFERENCE: $ref"
    missing=1
  fi
done
[ $missing -eq 0 ] && echo "Reference resolution: OK"

echo "=== All Asset Checks Passed ==="
```

### 2. Contract Verification (Pre-Release)

Run the contract verification script from [compatibility-contracts.md](./compatibility-contracts.md#automated-checks-recommended-for-ci).

### 3. Boot Visual Verification (Release Gate)

**Required Environment:**
- Virtual machine (QEMU/KVM, VirtualBox, VMware) or physical hardware
- Target GRUB2 version (match distribution target)
- Both BIOS and UEFI firmware modes if possible

**Procedure:**
1. Install theme to test system:
   ```bash
   sudo cp -r Nuci /usr/share/grub/themes/
   sudo chmod -R 755 /usr/share/grub/themes/Nuci
   sudo find /usr/share/grub/themes/Nuci -type f -exec chmod 644 {} \;
   sudo sed -i 's|^#\?GRUB_THEME=.*|GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt|' /etc/default/grub
   sudo grub-mkconfig -o /boot/grub/grub.cfg
   ```

2. Reboot and verify each visual element:

| Element | Expected | Verification Method |
|---------|----------|---------------------|
| Background | `background.png` fills screen | Visual inspection |
| Menu position | 36% left, 30% top | Visual; matches `theme.txt` |
| Menu size | 28% width, 60% height | Visual |
| Menu font | Unifont Regular 16 | Glyph recognition |
| Item colour (unselected) | #cccccc (light grey) | Visual |
| Item colour (selected) | #ffffff (white) | Visual |
| Selection highlight | 8-directional sprites | Navigate with ↑↓; observe sprite changes |
| Item height | 32px | Visual consistency |
| Item spacing | 16px | Visual consistency |
| Timeout label | 82% top, 35% left, centered | Visual |
| Timeout text | "Booting in N seconds" | Visual; countdown works |
| Terminal box (press `c`) | 9-slice frame renders | Enter command line; verify frame |
| Icons | Distribution icons show | Check boot entries with known classes |

3. Test both BIOS and UEFI boot if possible.

4. Test multiple screen resolutions (1024×768, 1920×1080, 3840×2160).

### 4. Regression Verification

**Baseline:** Screenshots or screen recordings from previous verified release.

**Comparison:** Visual diff against baseline for:
- Background rendering
- Menu layout and positioning
- Selection sprite animation
- Terminal box frame
- Icon display

## Coverage Gaps

| Gap | Impact | Mitigation |
|-----|--------|------------|
| No automated test suite | Human error possible in verification | Checklist-driven manual verification |
| No multi-resolution CI | Layout breaks at untested resolutions | Test common resolutions manually |
| No multi-firmware CI | BIOS/UEFI differences undetected | Test both when possible |
| No GRUB2 version matrix | Upstream breaks undetected | Test on minimum supported GRUB2 |
| No asset optimisation check | Large assets slow boot | Manual file size review |
| No accessibility verification | Colour contrast, font readability | Manual review against WCAG |
| No localisation testing | Non-ASCII boot entries | Test with Unicode entry names |

## Test Constraints

| Constraint | Description |
|------------|-------------|
| **Boot-time only** | Theme only exercised during GRUB2 boot; no runtime testing |
| **Hardware-dependent** | Rendering varies by firmware, GPU, display |
| **No headless verification** | Requires framebuffer; cannot test in container/CI easily |
| **Single-shot** | Each boot test requires full reboot cycle |
| **No unit tests** | No testable code units; only integration (boot) test |

## Recommended Test Matrix

| Dimension | Values to Test |
|-----------|----------------|
| Firmware | BIOS (CSM), UEFI |
| GRUB2 Version | 2.04, 2.06, 2.12 |
| Resolution | 1024×768, 1366×768, 1920×1080, 2560×1440, 3840×2160 |
| Aspect Ratio | 4:3, 16:9, 16:10, 21:9 |
| Boot Entries | Linux (multiple classes), Windows, UEFI, Memtest, Shutdown, Reboot |
| Keyboard Navigation | ↑, ↓, Enter, `c` (command line), `e` (edit) |

## Release Verification Checklist

- [ ] Asset integrity checks pass
- [ ] Contract verification passes
- [ ] Boot test on BIOS VM (1920×1080)
- [ ] Boot test on UEFI VM (1920×1080)
- [ ] Boot test on second resolution (1024×768)
- [ ] Selection sprites render in all 8 directions
- [ ] Terminal box 9-slice renders without gaps
- [ ] Icons display for configured boot entries
- [ ] Timeout label counts down correctly
- [ ] No render artifacts or missing assets
- [ ] Permissions correct on installed files
- [ ] `grub-mkconfig` embeds theme path correctly
- [ ] Documentation updated if visual changes

## Future Test Infrastructure (If Needed)

If automated testing becomes necessary:

1. **QEMU + OVMF** for UEFI boot automation
2. **GRUB2 theme preview tool** (if developed) for headless rendering
3. **Image comparison** (perceptual diff) against baseline screenshots
4. **Asset validation** in pre-commit hook
5. **Contract verification** in CI pipeline