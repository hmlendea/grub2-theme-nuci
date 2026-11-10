# API Usage Examples — GRUB2 Theme Nuci

Practical examples for working with the theme descriptor and assets.

## Theme Descriptor Examples

### Minimal Theme

```text
# Minimal GRUB2 theme
background: "background.png"
font: "Unifont Regular 16"
terminal_box: "terminal_box_*.png"
select: "select_*.png"

+ boot_menu {
    left = 10%
    top = 10%
    width = 80%
    height = 80%
}

+ label {
    left = 10%
    top = 90%
    text = "Booting in %d seconds"
}
```

### Custom Colours

```text
background: "background.png"
font: "Unifont Regular 16"
color_normal: #888888
color_highlight: #ffffff
color_border: #444444
terminal_box: "terminal_box_*.png"
select: "select_*.png"

+ boot_menu {
    left = 20%
    top = 20%
    width = 60%
    height = 60%
    item_color = #aaaaaa
    item_color_highlight = #ffffff
    item_height = 40
    item_spacing = 20
}
```

### Custom Font

```text
background: "background.png"
font: "DejaVu Sans Bold 14"
terminal_box: "terminal_box_*.png"
select: "select_*.png"

+ boot_menu {
    left = 15%
    top = 15%
    width = 70%
    height = 70%
    item_font = "DejaVu Sans Regular 14"
}
```

### Progress Bar

```text
background: "background.png"
font: "Unifont Regular 16"
terminal_box: "terminal_box_*.png"
select: "select_*.png"

+ boot_menu {
    left = 10%
    top = 10%
    width = 80%
    height = 70%
}

+ label {
    left = 10%
    top = 85%
    width = 80%
    text = "Booting in %d seconds"
}

+ progress_bar {
    left = 10%
    top = 92%
    width = 80%
    height = 3%
    bg_color = #222222
    fg_color = #00aa00
    border_color = #444444
}
```

## Asset Replacement Examples

### Replace Background

```bash
# 1. Create new background (1920x1080 recommended)
# 2. Replace file
cp my-background.png Nuci/background.png

# 3. Verify
file Nuci/background.png
# PNG image data, 1920 x 1080, 8-bit/color RGBA, non-interlaced

# 4. Reinstall
sudo cp -r Nuci /usr/share/grub/themes/
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### Replace Selection Sprites

```bash
# All 8 must be replaced together for consistency
# Create: select_c.png, select_n.png, select_ne.png, select_e.png,
#         select_se.png, select_s.png, select_sw.png, select_w.png

for f in select_{c,n,ne,e,se,s,sw,w}.png; do
  cp new-sprites/$f Nuci/$f
done

# Verify count
ls Nuci/select_*.png | wc -l  # Must be 8

# Reinstall
sudo cp -r Nuci /usr/share/grub/themes/
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### Add Distribution Icon

```bash
# 1. Determine class (e.g., "manjaro")
# 2. Create icon (128x128, transparent PNG)
# 3. Place in icons directory
cp manjaro.png Nuci/icons/manjaro.png

# 4. Verify
ls Nuci/icons/manjaro.png
file Nuci/icons/manjaro.png

# 5. Reinstall (no theme.txt change needed)
sudo cp -r Nuci /usr/share/grub/themes/
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### Modify Menu Layout

Edit `Nuci/theme.txt`:

```text
# Move menu to left side, make wider
+ boot_menu {
    left = 5%        # Was 36%
    top = 20%        # Was 30%
    width = 40%      # Was 28%
    height = 60%     # Same
    item_height = 32
    item_spacing = 16
}
```

### Change Timeout Label Position

```text
+ label {
    left = 50%       # Center horizontally
    top = 90%        # Near bottom
    width = 50%
    align = "center"
    font = "Unifont Regular 16"
    color = #888888
    text = "Booting in %d seconds"
}
```

## Installation Examples

### Manual Installation (Any Distribution)

```bash
#!/bin/bash
# install-theme.sh

THEME_DIR="/usr/share/grub/themes/Nuci"

# Copy theme
sudo cp -r Nuci "$THEME_DIR"

# Set permissions
sudo find "$THEME_DIR" -type d -exec chmod 755 {} \;
sudo find "$THEME_DIR" -type f -exec chmod 644 {} \;

# Configure GRUB
sudo sed -i 's|^#\?GRUB_THEME=.*|GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt|' /etc/default/grub

# Optional: Set resolution
# sudo sed -i 's|^#\?GRUB_GFXMODE=.*|GRUB_GFXMODE=1920x1080|' /etc/default/grub

# Regenerate config
sudo grub-mkconfig -o /boot/grub/grub.cfg

echo "Theme installed. Reboot to test."
```

### AUR Package Build (Arch Linux)

```bash
# PKGBUILD
pkgname=grub2-theme-nuci
pkgver=1.0.0
pkgrel=1
pkgdesc="Nuci GRUB2 theme"
arch=('any')
url="https://github.com/hmlendea/grub2-theme-nuci"
license=('GPL3')
source=("git+https://github.com/hmlendea/grub2-theme-nuci.git")
sha256sums=('SKIP')

package() {
  cd grub2-theme-nuci
  install -dm755 "$pkgdir/usr/share/grub/themes/Nuci"
  cp -r Nuci/* "$pkgdir/usr/share/grub/themes/Nuci/"
  find "$pkgdir/usr/share/grub/themes/Nuci" -type f -exec chmod 644 {} \;
  find "$pkgdir/usr/share/grub/themes/Nuci" -type d -exec chmod 755 {} \;
}
```

### Verify Installation

```bash
#!/bin/bash
# verify-install.sh

echo "=== Installation Verification ==="

# Check theme directory
if [ -d /usr/share/grub/themes/Nuci ]; then
  echo "✓ Theme directory exists"
  ls -la /usr/share/grub/themes/Nuci/
else
  echo "✗ Theme directory missing"
  exit 1
fi

# Check theme.txt
if [ -f /usr/share/grub/themes/Nuci/theme.txt ]; then
  echo "✓ theme.txt exists"
else
  echo "✗ theme.txt missing"
  exit 1
fi

# Check GRUB_THEME
if grep -q 'GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt' /etc/default/grub; then
  echo "✓ GRUB_THEME configured"
else
  echo "✗ GRUB_THEME not set correctly"
  exit 1
fi

# Check grub.cfg
if grep -q 'theme.*Nuci' /boot/grub/grub.cfg; then
  echo "✓ Theme referenced in grub.cfg"
else
  echo "✗ Theme not in grub.cfg (run grub-mkconfig)"
  exit 1
fi

# Check permissions
bad_perms=$(find /usr/share/grub/themes/Nuci -type d ! -perm 755 -o -type f ! -perm 644 | wc -l)
if [ $bad_perms -eq 0 ]; then
  echo "✓ Permissions correct"
else
  echo "✗ Permission issues: $bad_perms"
fi

echo "=== Verification Complete ==="
```

## Testing Examples

### Quick Asset Check

```bash
#!/bin/bash
# quick-check.sh

echo "Asset counts:"
echo "  Background: $(ls Nuci/background.png 2>/dev/null | wc -l)/1"
echo "  Selection:  $(ls Nuci/select_*.png 2>/dev/null | wc -l)/8"
echo "  Terminal:   $(ls Nuci/terminal_box_*.png 2>/dev/null | wc -l)/9"
echo "  Icons:      $(ls Nuci/icons/*.png 2>/dev/null | wc -l)"

echo
echo "PNG validity:"
for f in Nuci/*.png Nuci/icons/*.png; do
  file "$f" | grep -q 'PNG image data' && echo "  ✓ $f" || echo "  ✗ $f"
done

echo
echo "Theme.txt references:"
for ref in $(grep -oP '(?<=: "|= ")[^"]+\.png' Nuci/theme.txt | sed 's/\*//g' | sort -u); do
  ls Nuci/$ref 1>/dev/null 2>&1 && echo "  ✓ $ref" || echo "  ✗ $ref (MISSING)"
done
```

### VM Test Script (QEMU)

```bash
#!/bin/bash
# test-in-qemu.sh

# Requires: qemu-system-x86_64, OVMF (UEFI), grub2 installed

DISK_IMG="test-disk.img"
THEME_DIR="/usr/share/grub/themes/Nuci"

# Create test disk
qemu-img create -f qcow2 "$DISK_IMG" 4G

# Install minimal system with GRUB2 and theme (simplified)
# This is a conceptual example; actual implementation requires
# a full OS install or pre-built test image

echo "Boot test VM with:"
echo "  qemu-system-x86_64 -bios /usr/share/ovmf/OVMF.fd -drive file=$DISK_IMG,format=qcow2"
echo ""
echo "Or for BIOS:"
echo "  qemu-system-x86_64 -drive file=$DISK_IMG,format=qcow2"
```

## Customisation Workflow

```bash
#!/bin/bash
# customise-theme.sh

# 1. Fork/clone repository
git clone https://github.com/hmlendea/grub2-theme-nuci.git my-nuci-theme
cd my-nuci-theme

# 2. Make changes
# - Edit Nuci/theme.txt
# - Replace Nuci/background.png
# - Replace Nuci/select_*.png
# - Replace Nuci/terminal_box_*.png
# - Add/remove Nuci/icons/*.png

# 3. Test locally
./quick-check.sh

# 4. Install from fork
sudo cp -r Nuci /usr/share/grub/themes/Nuci-custom
sudo sed -i 's|GRUB_THEME=.*|GRUB_THEME=/usr/share/grub/themes/Nuci-custom/theme.txt|' /etc/default/grub
sudo grub-mkconfig -o /boot/grub/grub.cfg

# 5. Reboot and verify

# 6. Commit and push
git add -A
git commit -m "Customise theme: new background, updated colours"
git push origin main
```

## Related Documents

- [theme-descriptor.md](../api-reference/theme-descriptor.md) — Complete descriptor reference
- [asset-specifications.md](../api-reference/asset-specifications.md) — Asset requirements
- [icon-mapping.md](../api-reference/icon-mapping.md) — Icon conventions
- [build-and-deployment.md](../build-and-deployment.md) — Installation procedures
- [testing.md](../testing.md) — Verification procedures