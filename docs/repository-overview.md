# Repository Overview — GRUB2 Theme Nuci

## Purpose

This repository provides a complete, ready-to-install GRUB2 theme named **Nuci**. The theme implements a Vimix-inspired aesthetic for the GRUB2 boot menu, including a background image, styled boot menu with selection highlights, a 9-slice terminal box, and a set of distribution-specific icons.

## Scope

**In scope:**
- Theme descriptor (`Nuci/theme.txt`) defining layout, colours, fonts, and asset references
- All raster assets (PNG) referenced by the descriptor
- Installation documentation and community health files
- Distribution packaging support (AUR PKGBUILD)

**Out of scope:**
- GRUB2 bootloader itself (upstream dependency)
- Build tools, compilers, or CI pipelines
- Runtime configuration or theming API
- Multiple theme variants or resolution-specific assets
- Automated testing infrastructure

## Principal Capabilities

1. **Boot menu theming** — Custom background, menu position/size, fonts, colours
2. **Selection highlighting** — 8-directional sprite-based selection indicator
3. **Terminal styling** — 9-slice scaled terminal box for command-line editing
4. **Distribution icons** — Class-specific icons for boot entries (Arch, Debian, Fedora, Ubuntu, etc.)
5. **Simple installation** — Single directory copy + GRUB configuration update

## Entry Points

| Entry Point | Type | Description |
|-------------|------|-------------|
| `Nuci/theme.txt` | Theme descriptor | Authoritative source for all theme parameters; parsed by GRUB2 at boot |
| `README.md` | User documentation | Installation instructions, feature overview, links to policy documents |
| `CONTRIBUTING.md` | Contributor guide | Issue reporting, enhancement suggestions, PR process |
| `SECURITY.md` | Security policy | Vulnerability reporting, supported versions, disclosure process |
| `ARCHITECTURE.md` | Architecture documentation | System context, components, flows, dependencies, constraints |

## Repository Type

**Static asset package** — No executable code, no build process, no runtime. The repository is a data package consumed by GRUB2 at boot time.

## Target Audience

- **End users** — Install theme for customised boot menu
- **Distribution packagers** — Package theme for AUR, official repos, etc.
- **Theme customisers** — Modify assets or descriptor for personal variants
- **Contributors** — Submit improvements, new icons, bug fixes

## Key Integrations

| Integration | Direction | Mechanism |
|-------------|-----------|-----------|
| GRUB2 bootloader | Inbound (consumes) | Reads `theme.txt` and PNG assets at boot |
| `grub-mkconfig` | Inbound (generates config) | Embeds `GRUB_THEME` path into `grub.cfg` |
| Distribution packaging | Outbound (installed by) | Copies `Nuci/` to `/usr/share/grub/themes/` |
| System configuration | Inbound (configured by) | `/etc/default/grub` sets `GRUB_THEME` |

## Operational Characteristics

- **Deployment:** Atomic directory copy (`cp -r Nuci /usr/share/grub/themes/`)
- **Configuration:** Single line in `/etc/default/grub` + `grub-mkconfig`
- **Runtime:** Zero processes, zero memory footprint (assets loaded by GRUB2)
- **Network:** Fully offline; no network access at any stage
- **State:** No persistent state, no caches, no databases
- **Updates:** Replace directory + re-run `grub-mkconfig`