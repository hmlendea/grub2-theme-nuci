# Change Guide — GRUB2 Theme Nuci

Procedures for making changes to the theme package safely.

## Change Categories

| Category | Examples | Risk | Review Required |
|----------|----------|------|-----------------|
| **Asset Replacement** | New background, updated sprites, new icons | Low | Visual verification |
| **Layout Adjustment** | Menu position, size, spacing, colours | Medium | Boot test |
| **Descriptor Modification** | New properties, blocks, value changes | Medium | Boot test + spec check |
| **Structural Change** | Add/remove asset types, change patterns | High | Full regression |
| **Build/Install Change** | Packaging, paths, permissions | Medium | Install verification |

## Pre-Change Checklist

- [ ] Read [compatibility-contracts.md](../compatibility-contracts.md) for affected contracts
- [ ] Run asset integrity checks: `./verify-assets.sh`
- [ ] Run contract verification: `./verify-contracts.sh`
- [ ] Identify all affected files
- [ ] Plan rollback procedure

## Asset Replacement Procedure

### Background Image

```bash
# 1. Prepare new background (1920x1080 recommended, PNG)
cp new-background.png Nuci/background.png

# 2. Verify
file Nuci/background.png
# PNG image data, 1920 x 1080, 8-bit/color RGBA, non-interlaced

# 3. Test locally (if test environment available)
# 4. Commit
git add Nuci/background.png
git commit -m "Update background image"
```

### Selection Sprites (All 8 Together)

```bash
# 1. Prepare all 8 sprites
for f in select_{c,n,ne,e,se,s,sw,w}.png; do
  cp new-sprites/$f Nuci/$f
done

# 2. Verify count and validity
ls Nuci/select_*.png | wc -l  # Must be 8
for f in Nuci/select_*.png; do file "$f"; done

# 3. Test boot visual verification critical
# 4. Commit
git add Nuci/select_*.png
git commit -m "Update selection sprites (all 8)"
```

### Terminal Box Sprites (All 9 Together)

```bash
# 1. Prepare all 9 sprites
for f in terminal_box_{c,n,ne,e,se,s,sw,w,nw}.png; do
  cp new-sprites/$f Nuci/$f
done

# 2. Verify count and validity
ls Nuci/terminal_box_*.png | wc -l  # Must be 9
for f in Nuci/terminal_box_*.png; do file "$f"; done

# 3. Test command line mode (press 'c' at boot)
# 4. Commit
git add Nuci/terminal_box_*.png
git commit -m "Update terminal box sprites (all 9)"
```

### Add Distribution Icon

```bash
# 1. Determine exact class name from target distribution
# 2. Create icon (128x128, transparent PNG)
cp distro-icon.png Nuci/icons/distro.png

# 3. Verify
file Nuci/icons/distro.png
ls Nuci/icons/distro.png

# 4. No theme.txt change needed
# 5. Commit
git add Nuci/icons/distro.png
git commit -m "Add icon for distro (class: distro)"
```

## Layout Adjustment Procedure

### Modify Menu Position/Size

```bash
# 1. Edit theme.txt
# Change boot_menu block percentages
# left, top, width, height (0-100%)

# 2. Verify syntax
# No formal validator; check for:
# - Percentages with % suffix
# - Values 0-100
# - No typos in property names

# 4. Test at multiple resolutions
# 5. Commit
git add Nuci/theme.txt
git commit -m "Adjust menu layout: left=10%, width=50%"
```

### Modify Colours

```bash
# 1. Edit theme.txt
# Change colour properties (#RRGGBB format)
# color_normal, color_highlight, item_color, etc.

# 2. Verify contrast (WCAG AA: 4.5:1 for text)
# 3. Test visual verification
# 4. Commit
git add Nuci/theme.txt
git commit -m "Update colour scheme: higher contrast"
```

### Modify Font

```bash
# 1. Edit theme.txt
# font: "Family Style Size"
# Ensure font available to GRUB2 at boot

# 2. Test glyph rendering (special characters, Unicode)
# 3. Commit
git add Nuci/theme.txt
git commit -m "Change font to DejaVu Sans 14"
```

## Descriptor Modification Procedure

### Add Property

```bash
# 1. Edit theme.txt
# Add known GRUB2 property (check spec)
# Example: item_padding = 10

# 2. Verify GRUB2 version compatibility
# 3. Test
# 4. Commit
```

### Add Block

```bash
# 1. Edit theme.txt
# Add progress_bar block for boot progress
# 2. Test progress bar renders during boot
# 3. Commit
```

## Structural Change Procedure

### Add New Asset Type

**High risk — requires:**
1. GRUB2 spec support verification
2. Theme.txt pattern addition
3. Asset creation pipeline
4. Installation script updates
5. Documentation updates
6. Full regression test

### Change Sprite Pattern

**Not recommended — GRUB2 expects:**
- `select_*.png` (8 specific suffixes)
- `terminal_box_*.png` (9 specific suffixes)

Changing pattern breaks compatibility.

## Testing After Changes

### Mandatory Tests

| Change Type | Tests Required |
|-------------|----------------|
| Asset replacement | Asset integrity, boot visual |
| Layout adjustment | Boot visual at 2+ resolutions |
| Descriptor mod | Boot visual, spec compliance |
| Structural | Full regression matrix |

### Test Matrix

| Resolution | Firmware | GRUB2 Version | Boot Entries |
|------------|----------|---------------|--------------|
| 1920×1080 | UEFI | 2.06 | Linux, Windows, UEFI |
| 1024×768 | BIOS | 2.04 | Linux, Memtest |
| 3840×2160 | UEFI | 2.12 | Linux, Shutdown |

## Rollback Procedure

```bash
# 1. Revert commit
git revert HEAD

# 2. Reinstall previous version
sudo cp -r Nuci /usr/share/grub/themes/
sudo grub-mkconfig -o /boot/grub/grub.cfg

# 3. Verify boot
```

## Release Procedure

```bash
# 1. Ensure all tests pass
# 2. Update version tag
git tag -a v1.2.0 -m "Release v1.2.0: new icons, colour update"

# 3. Push tag
git push origin v1.2.0

# 4. GitHub Release: attach notes, assets if needed
# 5. AUR package update (separate process)
```

## Documentation Updates

After any user-visible change, update:

- [ ] README.md (if new feature)
- [ ] docs/api-usage-examples.md (if new pattern)
- [ ] docs/asset-specifications.md (if new asset type)
- [ ] docs/icon-mapping.md (if new icon)
- [ ] docs/compatibility-contracts.md (if contract changed)
- [ ] docs/troubleshooting.md (if new failure mode)
- [ ] docs/faq.md (if new common question)

## Related Documents

- [compatibility-contracts.md](../compatibility-contracts.md) — Contracts to preserve
- [testing.md](../testing.md) — Test procedures
- [troubleshooting.md](../troubleshooting.md) — Failure diagnosis
- [build-and-deployment.md](../build-and-deployment.md) — Installation