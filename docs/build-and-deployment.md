# Build and Deployment — GRUB2 Theme Nuci

This document describes the installation procedure, deployment unit, filesystem requirements, and packaging scripts.

## Deployment Model

**Type:** Static asset deployment — no build, no compilation, no runtime.

**Deployment Unit:** The entire `Nuci/` directory copied atomically.

**Target Location:** `/usr/share/grub/themes/Nuci/`

**Permissions:**
- Directories: `755` (rwxr-xr-x)
- Files: `644` (rw-r--r--)

## Installation Procedure

### Manual Installation (End User)

```bash
# 1. Copy theme directory to system GRUB themes folder
sudo cp -r Nuci /usr/share/grub/themes/

# 2. Set permissions (defensive; cp -r usually preserves)
sudo chmod -R 755 /usr/share/grub/themes/Nuci
sudo find /usr/share/grub/themes/Nuci -type f -exec chmod 644 {} \;

# 3. Configure GRUB to use the theme
sudo sed -i 's|^#\?GRUB_THEME=.*|GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt|' /etc/default/grub

# 4. Regenerate GRUB configuration
sudo grub-mkconfig -o /boot/grub/grub.cfg

# 5. Reboot to verify
```

### Automated Installation (Distribution Package)

**AUR PKGBUILD Example:**
```bash
# Maintainer: Your Name <email@example.com>
pkgname=grub2-theme-nuci
pkgver=1.0.0
pkgrel=1
pkgdesc="Vimix-inspired GRUB2 theme"
arch=('any')
url="https://github.com/hmlendea/grub2-theme-nuci"
license=('GPL3')
source=("https://github.com/hmlendea/grub2-theme-nuci/archive/v${pkgver}.tar.gz")
sha256sums=('...')

package() {
  cd "grub2-theme-nuci-${pkgver}"
  install -dm755 "$pkgdir/usr/share/grub/themes/Nuci"
  cp -r Nuci/* "$pkgdir/usr/share/grub/themes/Nuci/"
}
```

**Post-Install Message (recommended):**
```
==> GRUB2 Theme Nuci installed.
==> To enable, add to /etc/default/grub:
    GRUB_THEME="/usr/share/grub/themes/Nuci/theme.txt"
==> Then run: grub-mkconfig -o /boot/grub/grub.cfg
```

## Filesystem Requirements

| Requirement | Specification |
|-------------|---------------|
| Target filesystem | Any POSIX-compliant (ext4, btrfs, xfs, etc.) |
| Space required | ~2-5 MB (depends on asset count/size) |
| Permissions | Root write to `/usr/share/grub/themes/` |
| GRUB2 access | GRUB2 must read `/usr/share/grub/themes/Nuci/` at boot (early userspace) |
| Initramfs | Not required (theme loaded from root filesystem after pivot) |

## Deployment Verification

### Post-Install Checks

```bash
# 1. Verify theme directory structure
ls -la /usr/share/grub/themes/Nuci/
# Should show: theme.txt, background.png, select_*.png, terminal_box_*.png, icons/

# 2. Verify asset completeness
test -f /usr/share/grub/themes/Nuci/theme.txt && echo "theme.txt OK"
ls /usr/share/grub/themes/Nuci/select_*.png | wc -l  # Should be 8
ls /usr/share/grub/themes/Nuci/terminal_box_*.png | wc -l  # Should be 9
ls /usr/share/grub/themes/Nuci/icons/*.png | wc -l  # Should be >0

# 3. Verify permissions
find /usr/share/grub/themes/Nuci -type d ! -perm 755 | wc -l  # Should be 0
find /usr/share/grub/themes/Nuci -type f ! -perm 644 | wc -l  # Should be 0

# 4. Verify GRUB configuration
grep GRUB_THEME /etc/default/grub
# Should show: GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt

grep 'set theme=' /boot/grub/grub.cfg
# Should show: set theme=/usr/share/grub/themes/Nuci/theme.txt
```

### Boot Verification

1. Reboot system
2. Observe GRUB2 boot menu:
   - Background image renders
   - Menu positioned per `theme.txt` (36% left, 30% top, 28% width, 60% height)
   - Menu items use `Unifont Regular 16`, colour `#cccccc`
   - Selected item highlights with `select_*.png` sprites, white text `#ffffff`
   - Timeout label at 82% top, centered, "Booting in N seconds"
   - Press `c` for command line: terminal box renders with 9-slice sprites
   - Boot entries show distribution icons from `icons/`

## Update Procedure

### Manual Update

```bash
# 1. Backup current theme (optional)
sudo cp -r /usr/share/grub/themes/Nuci /usr/share/grub/themes/Nuci.backup

# 2. Replace with new version
sudo cp -r Nuci /usr/share/grub/themes/

# 3. Fix permissions
sudo chmod -R 755 /usr/share/grub/themes/Nuci
sudo find /usr/share/grub/themes/Nuci -type f -exec chmod 644 {} \;

# 4. Regenerate GRUB config (required if theme.txt changed)
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

### Package Update

Distribution package manager handles file replacement. Post-upgrade hook should run `grub-mkconfig`.

## Uninstallation

```bash
# 1. Remove theme directory
sudo rm -rf /usr/share/grub/themes/Nuci

# 2. Remove GRUB_THEME from config (or comment out)
sudo sed -i 's|^GRUB_THEME=.*|#GRUB_THEME=|' /etc/default/grub

# 3. Regenerate GRUB config
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## CI/CD Considerations

**No CI pipeline exists** — this is a static asset repository. If CI is added:

| Stage | Purpose |
|-------|---------|
| Asset validation | Verify all `theme.txt` references resolve; PNG validity; sprite counts |
| Permission check | Ensure no executable bits on assets |
| Package build | Test PKGBUILD/debian packaging in clean chroot |
| Boot test | VM boot test (requires GRUB2, firmware emulation) |

## Environment Matrix

| Environment | Supported | Notes |
|-------------|-----------|-------|
| BIOS (legacy) | Yes | GRUB2 VESA framebuffer |
| UEFI | Yes | GRUB2 EFI GOP framebuffer |
| Secure Boot | Yes | Theme is data; no signing required |
| LUKS / encrypted /boot | Yes | Theme on root FS; unlocked before GRUB2 theme load |
| Btrfs / ZFS root | Yes | Standard filesystem access |
| PXE / network boot | No | Theme requires local filesystem |

## Rollback

If new theme version causes issues:

```bash
# Quick rollback
sudo rm -rf /usr/share/grub/themes/Nuci
sudo mv /usr/share/grub/themes/Nuci.backup /usr/share/grub/themes/Nuci
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

Or reinstall previous package version via package manager.