# Configuration — GRUB2 Theme Nuci

Configuration options for the theme and its integration points.

## Theme Configuration (theme.txt)

All theme configuration is in `Nuci/theme.txt`. See [theme-descriptor.md](../api-reference/theme-descriptor.md) for complete reference.

### Key Configurable Sections

| Section | Properties | Description |
|---------|------------|-------------|
| Global | `background`, `font`, `color_normal`, `color_highlight`, `terminal_box`, `select` | Core theme settings |
| `boot_menu` | `left`, `top`, `width`, `height`, `item_*`, `icon_*` | Menu layout and styling |
| `label` | `left`, `top`, `width`, `height`, `align`, `font`, `color`, `text`, `visible` | Timeout/label display |
| `progress_bar` | `left`, `top`, `width`, `height`, `bg_color`, `fg_color`, `border_color`, `show_text` | Boot progress (optional) |

### Common Customisations

```text
# Move menu to left
+ boot_menu {
    left = 5%
    width = 40%
}

# Change colours
color_normal: #888888
color_highlight: #ffffff
+ boot_menu {
    item_color = #aaaaaa
    item_color_highlight = #ffffff
}

# Change font
font: "DejaVu Sans Regular 14"
+ boot_menu {
    item_font = "DejaVu Sans Regular 14"
}

# Disable timeout label
+ label {
    visible = false
}
```

## GRUB2 Configuration (/etc/default/grub)

Theme activation and video settings controlled via GRUB2 configuration.

### Required Settings

```bash
# Enable theme
GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt

# Set video resolution (match display native)
GRUB_GFXMODE=1920x1080
# Or auto-detect:
# GRUB_GFXMODE=auto

# Preserve resolution for kernel
GRUB_GFXPAYLOAD_LINUX=keep
```

### Optional Settings

```bash
# Timeout (seconds)
GRUB_TIMEOUT=5

# Default boot entry (index or 'saved')
GRUB_DEFAULT=0

# Save last selection
# GRUB_SAVEDEFAULT=true

# Disable submenu (simpler menu)
# GRUB_DISABLE_SUBMENU=true

# Terminal output (console for boot messages)
# GRUB_TERMINAL_OUTPUT=console
```

### Applying Changes

```bash
# After editing /etc/default/grub
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Distribution-Specific Configuration

### Arch Linux / AUR

```bash
# Install via AUR
yay -S grub2-theme-nuci
# or
paru -S grub2-theme-nuci

# Theme installed to /usr/share/grub/themes/Nuci/
# Configure GRUB_THEME manually or via grub-theme-manager
```

### Manual Installation (Any Distribution)

```bash
# Copy theme
sudo cp -r Nuci /usr/share/grub/themes/

# Set permissions
sudo find /usr/share/grub/themes/Nuci -type d -exec chmod 755 {} \;
sudo find /usr/share/grub/themes/Nuci -type f -exec chmod 644 {} \;

# Configure GRUB
sudo sed -i 's|^#\?GRUB_THEME=.*|GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt|' /etc/default/grub

# Optional: Set resolution
sudo sed -i 's|^#\?GRUB_GFXMODE=.*|GRUB_GFXMODE=1920x1080|' /etc/default/grub

# Regenerate
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Firmware-Specific Configuration

### UEFI Systems

```bash
# Usually works with auto-detection
GRUB_GFXMODE=auto
GRUB_GFXPAYLOAD_LINUX=keep

# If specific resolution needed
GRUB_GFXMODE=1920x1080x32
```

### BIOS Systems

```bash
# May need explicit mode
GRUB_GFXMODE=1024x768
# Or test supported modes with `vbeinfo` at GRUB command line
```

## Icon Configuration

Icons require no configuration. GRUB2 auto-discovers:

```
Boot entry class: --class archlinux
Icon file: icons/archlinux.png
```

To add icon: place `{class}.png` in `Nuci/icons/`.

## Environment Variables

No environment variables used by the theme itself.

GRUB2 uses:
- `GRUB_THEME` (set in grub.cfg)
- `GRUB_GFXMODE` (set in grub.cfg)
- `GRUB_TIMEOUT` (set in grub.cfg)

## Configuration Validation

```bash
# Verify theme.txt syntax (basic)
grep -E '^(background|font|terminal_box|select):' Nuci/theme.txt

# Verify GRUB_THEME in grub.cfg
grep GRUB_THEME /boot/grub/grub.cfg

# Verify video mode in grub.cfg
grep GRUB_GFXMODE /boot/grub/grub.cfg
```

## Related Documents

- [theme-descriptor.md](../api-reference/theme-descriptor.md) — Complete theme.txt reference
- [build-and-deployment.md](../build-and-deployment.md) — Installation procedures
- [troubleshooting.md](../troubleshooting.md) — Configuration issues
- [faq.md](../faq.md) — Common configuration questions