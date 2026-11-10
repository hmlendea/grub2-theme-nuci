# Dependencies — GRUB2 Theme Nuci

Documents all dependencies for building, installing, and running the theme.

## Runtime Dependencies

### GRUB2 (Required)

| Dependency | Version | Purpose |
|------------|---------|---------|
| GRUB2 | ≥ 2.04 | Theme engine consumer |

**Tested Versions:**
- GRUB 2.04 (baseline)
- GRUB 2.06 (common in current distributions)
- GRUB 2.12 (newer)

**Features Used:**
- Theme engine (`grub-core/normal/theme.c`)
- PNG decoder (`grub-core/fs/png.c`)
- Font renderer
- Video subsystem (VBE/GOP)
- Menu system (`grub-core/normal/menu.c`)

### Firmware Video Support

| Firmware | Interface | Required |
|----------|-----------|----------|
| BIOS | VBE (VESA BIOS Extensions) | Yes |
| UEFI | GOP (Graphics Output Protocol) | Yes |

### Font Availability

| Font | Source | Required |
|------|--------|----------|
| Unifont | Firmware (UEFI) or `grub-mkfont` (BIOS) | Yes |

**Note:** Theme specifies `"Unifont Regular 16"`. Must be available to GRUB2 at boot.

## Build-Time Dependencies

**None.** The theme is a static asset package with no build process.

| Tool | Purpose | Required |
|------|---------|----------|
| PNG editor (GIMP, Krita, etc.) | Asset creation/modification | For contributors |
| `file` command | PNG verification | For verification |
| `identify` (ImageMagick) | Dimension checking | For verification |
| `oxipng` / `pngcrush` | PNG optimisation | Optional |

## Installation Dependencies

### System Tools

| Tool | Purpose | Required |
|------|---------|----------|
| `cp` | Copy theme files | Yes |
| `chmod` | Set permissions | Yes |
| `sed` | Configure GRUB | Yes |
| `grub-mkconfig` | Regenerate grub.cfg | Yes |
| `grub-install` | Install GRUB (if needed) | For initial setup |

### Package Manager (Distribution-Specific)

| Distribution | Package | Provides |
|--------------|---------|----------|
| Arch Linux | `grub` | GRUB2, grub-mkconfig |
| Debian/Ubuntu | `grub2` / `grub-efi` | GRUB2 |
| Fedora | `grub2` | GRUB2 |
| openSUSE | `grub2` | GRUB2 |

## Development Dependencies

### For Contributors

| Tool | Purpose |
|------|---------|
| Git | Version control |
| GitHub CLI (`gh`) | PR/issue management |
| Text editor | theme.txt editing |
| PNG editor | Asset editing |
| QEMU + OVMF | UEFI boot testing |
| VirtualBox / VMware | BIOS/UEFI boot testing |

### For Documentation

| Tool | Purpose |
|------|---------|
| Markdown editor | Documentation editing |
| Mermaid renderer | Diagram viewing |

## AUR Package Dependencies (Arch Linux)

```bash
# PKGBUILD depends
depends=('grub')
makedepends=('git')
optdepends=('grub-theme-manager: GUI theme manager')
```

## Dependency Graph

```
GRUB2 Theme Nuci (static assets)
    │
    ├── Requires at runtime:
    │   ├── GRUB2 ≥ 2.04
    │   │   ├── PNG decoder (built-in)
    │   │   ├── Font renderer (built-in)
    │   │   ├── Video subsystem (VBE/GOP)
    │   │   └── Menu system
    │   ├── Firmware video (VBE or GOP)
    │   └── Unifont (firmware or grub-mkfont)
    │
    ├── Requires at install:
    │   ├── Coreutils (cp, chmod, sed)
    │   ├── GRUB2 tools (grub-mkconfig)
    │   └── Root privileges
    │
    └── Requires for development:
        ├── Git
        ├── PNG editor
        ├── QEMU/OVMF (testing)
        └── Markdown tools (docs)
```

## Version Compatibility Matrix

| GRUB2 Version | Theme Compatibility | Notes |
|---------------|---------------------|-------|
| 2.00 - 2.03 | Partial | Basic theme support; some properties missing |
| 2.04 - 2.05 | ✅ Full | Baseline compatibility |
| 2.06 - 2.11 | ✅ Full | Common in current distros |
| 2.12+ | ✅ Full | Newer features available |

## No External Library Dependencies

The theme does not depend on:
- External PNG libraries (GRUB2 has built-in decoder)
- FreeType (GRUB2 uses built-in font renderer)
- System libraries (no dynamic linking)
- Network services
- Daemons or background services

## Supply Chain

| Asset | Source | Verification |
|-------|--------|--------------|
| PNG assets | Created by maintainer | Manual review |
| Icons | Created by maintainer / community | Manual review |
| theme.txt | Written by maintainer | Syntax check |
| LICENSE | GPL-3.0-or-later | Standard |

## Related Documents

- [build-and-deployment.md](../build-and-deployment.md) — Installation procedures
- [compatibility-contracts.md](../compatibility-contracts.md) — GRUB2 version contract
- [troubleshooting.md](../troubleshooting.md) — Dependency-related issues
- [faq.md](../faq.md) — Common dependency questions