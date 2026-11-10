# Icon Mapping Reference — GRUB2 Theme Nuci

Specification for boot entry class ↔ icon filename mapping.

## Mapping Mechanism

GRUB2 automatically discovers icons by matching the boot entry's `class` field to PNG filenames in the theme's `icons/` directory.

**No configuration in `theme.txt` required.** The mapping is purely convention-based.

## Mapping Rule

```
Boot entry class:  --class <class_name>
Icon filename:     icons/<class_name>.png
Match:             Exact, case-sensitive
```

## Current Mappings

| Boot Entry Class | Icon File | Status | Notes |
|------------------|-----------|--------|-------|
| `archlinux` | `icons/archlinux.png` | ✅ Included | Arch Linux |
| `debian` | `icons/debian.png` | ✅ Included | Debian |
| `fedora` | `icons/fedora.png` | ✅ Included | Fedora |
| `ubuntu` | `icons/ubuntu.png` | ✅ Included | Ubuntu |
| `windows` | `icons/windows.png` | ✅ Included | Windows (any version) |
| `uefi` | `icons/uefi.png` | ✅ Included | UEFI Firmware Settings |
| `memtest` | `icons/memtest.png` | ✅ Included | Memtest86+ |
| `shutdown` | `icons/shutdown.png` | ✅ Included | System Shutdown |
| `reboot` | `icons/reboot.png` | ✅ Included | System Reboot |
| `linux` | `icons/linux.png` | ✅ Included | Generic Linux fallback |
| `unknown` | `icons/unknown.png` | ✅ Included | Unknown/Other fallback |

## Fallback Behaviour

GRUB2 uses a fallback chain when exact class match not found:

1. **Exact match:** `icons/<class>.png`
2. **Generic Linux:** `icons/linux.png` (for any `--class linux` entry)
3. **Unknown:** `icons/unknown.png` (final fallback)

The `linux` and `unknown` icons should always be present.

## Discovering Boot Entry Classes

To find the class for a distribution:

```bash
# From installed system's grub.cfg
grep -oP 'class\s+--class\s+\K\w+' /boot/grub/grub.cfg | sort -u

# Or from GRUB2 command line at boot:
# Press 'c' for command line, then:
# ls
# (examine menuentry lines)
```

Typical classes:
- Arch Linux: `archlinux`
- Debian: `debian`
- Fedora: `fedora`
- Ubuntu: `ubuntu`
- openSUSE: `opensuse`
- Manjaro: `manjaro`
- Linux Mint: `linuxmint`
- Gentoo: `gentoo`
- Alpine: `alpine`
- NixOS: `nixos`
- Void: `void`
- Windows: `windows`
- UEFI: `uefi`
- Memtest: `memtest`

## Adding a New Icon

### Prerequisites
1. Determine the exact `class` string used by the distribution's GRUB2 config
2. Create or obtain a suitable icon (see design guidelines below)
3. Name file exactly: `<class>.png`

### Procedure
```bash
# 1. Create icon (128x128 recommended, transparent background)
# 2. Place in icons directory
cp mydistro.png Nuci/icons/mydistro.png

# 3. Verify naming
ls Nuci/icons/mydistro.png

# 4. Reinstall theme and regenerate grub config
sudo cp -r Nuci /usr/share/grub/themes/
sudo grub-mkconfig -o /boot/grub/grub.cfg

# 5. Reboot and verify
```

### No theme.txt Changes Needed

The icon is auto-discovered. No modification to `theme.txt` required.

## Icon Design Guidelines

### Technical Requirements
- **Format:** PNG with alpha transparency
- **Dimensions:** Square (1:1 aspect ratio), 128×128 recommended
- **Colour Space:** sRGB
- **Background:** Transparent (not white/black)
- **File Size:** < 50 KB

### Visual Guidelines
- **Centred:** Logo centred with ~10% padding
- **Recognisable at 32×32:** Menu renders icons small
- **Consistent style:** Match existing icon visual language
- **Monochrome-friendly:** Recognisable if desaturated
- **No text:** Logo only; no distribution name text

### Testing Checklist
- [ ] Icon appears in boot menu for target distribution
- [ ] Icon visible at menu item height (32px)
- [ ] Transparency renders correctly (no white/black box)
- [ ] No visual clash with selection highlight
- [ ] Fallback works if icon missing

## Removing an Icon

```bash
# Simply delete the file
rm Nuci/icons/obsolete.png

# Reinstall and regenerate
sudo cp -r Nuci /usr/share/grub/themes/
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

GRUB2 will fall back to `linux.png` or `unknown.png`.

## Troubleshooting Icon Issues

| Issue | Cause | Resolution |
|-------|-------|------------|
| Icon not showing | Filename ≠ class | Verify exact case-sensitive match |
| Icon not showing | Wrong directory | Must be in `icons/` subdirectory |
| Icon not showing | Not reinstalled | Re-copy theme and run `grub-mkconfig` |
| Generic icon showing | No exact match | Add specific icon or check class name |
| Icon corrupted | Invalid PNG | Re-create with valid PNG |
| Icon has box | No transparency | Ensure alpha channel; transparent background |

## Verification Commands

```bash
# List all icons
ls -la Nuci/icons/

# Check for missing classes in grub.cfg
for class in $(grep -oP 'class\s+--class\s+\K\w+' /boot/grub/grub.cfg | sort -u); do
  if [ ! -f "Nuci/icons/${class}.png" ]; then
    echo "MISSING ICON FOR CLASS: $class"
  fi
done

# Verify all icon files are valid PNGs
for f in Nuci/icons/*.png; do
  file "$f" | grep -q 'PNG image data' || echo "INVALID: $f"
done
```

## Related Documents

- [asset-specifications.md](./asset-specifications.md) — Technical PNG requirements
- [components/theme-package.md](../components/theme-package.md) — Icons component deep-dive
- [troubleshooting.md](../troubleshooting.md#5-icons-not-displaying) — Icon troubleshooting