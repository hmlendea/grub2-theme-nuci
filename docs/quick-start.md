# Quick Start — GRUB2 Theme Nuci

Get the theme installed and visible in 5 minutes.

## Prerequisites

- Linux system with GRUB2 (any modern distribution)
- Root/sudo access
- Reboot capability

## Install (Manual)

```bash
# 1. Clone or download repository
git clone https://github.com/hmlendea/grub2-theme-nuci.git
cd grub2-theme-nuci

# 2. Copy theme to system
sudo cp -r Nuci /usr/share/grub/themes/

# 3. Set permissions
sudo find /usr/share/grub/themes/Nuci -type d -exec chmod 755 {} \;
sudo find /usr/share/grub/themes/Nuci -type f -exec chmod 644 {} \;

# 4. Configure GRUB
sudo sed -i 's|^#\?GRUB_THEME=.*|GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt|' /etc/default/grub

# 5. Regenerate GRUB config
sudo grub-mkconfig -o /boot/grub/grub.cfg

# 6. Reboot
sudo reboot
```

## Install (Arch Linux AUR)

```bash
# Using yay
yay -S grub2-theme-nuci

# Using paru
paru -S grub2-theme-nuci

# Then configure GRUB_THEME and regenerate
sudo sed -i 's|^#\?GRUB_THEME=.*|GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt|' /etc/default/grub
sudo grub-mkconfig -o /boot/grub/grub.cfg
sudo reboot
```

## Verify Installation

```bash
# Check theme directory
ls /usr/share/grub/themes/Nuci/

# Check GRUB_THEME setting
grep GRUB_THEME /etc/default/grub

# Check grub.cfg
grep theme /boot/grub/grub.cfg
```

## What You Should See

After reboot:

1. **Background image** fills the screen
2. **Boot menu** centred (36% left, 30% top, 28% wide, 60% tall)
3. **Distribution icons** next to boot entries
4. **Selection highlight** around current entry
5. **Timeout countdown** at bottom: "Booting in N seconds"

## Test Navigation

- `↑` / `↓` — Move selection (highlight follows)
- `Enter` — Boot selected entry
- `c` — Open command line (terminal box frame)
- `e` — Edit boot entry

## Troubleshooting

**Theme not showing?**
```bash
# Check GRUB_THEME
grep GRUB_THEME /etc/default/grub
# Should show: GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt

# Check theme files exist
ls /usr/share/grub/themes/Nuci/theme.txt

# Regenerate config
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

**Background black?**
```bash
# Check background file
file /usr/share/grub/themes/Nuci/background.png
# Should show: PNG image data
```

**Icons missing?**
```bash
# Check icons directory
ls /usr/share/grub/themes/Nuci/icons/
# Should show distribution icons
```

See [troubleshooting.md](./troubleshooting.md) for complete diagnosis.

## Next Steps

- [README.md](../README.md) — Project overview
- [configuration.md](./configuration.md) — Customise theme
- [api-usage-examples.md](./api-usage-examples.md) — Usage examples
- [build-and-deployment.md](./build-and-deployment.md) — Deployment details
- [CONTRIBUTING.md](../CONTRIBUTING.md) — Contribute changes