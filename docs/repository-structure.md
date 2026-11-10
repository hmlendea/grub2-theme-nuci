# Repository Structure — GRUB2 Theme Nuci

## Source Tree Layout

```
grub2-theme-nuci/
├── .github/
│   └── FUNDING.yml              # GitHub funding configuration
├── docs/                        # Generated documentation (this directory)
│   ├── INDEX.md                 # Master index and navigation
│   ├── architecture.md          # Architecture elaboration
│   ├── design-decisions.md      # Key design choices and rationale
│   ├── invariants.md            # System-wide invariants and contracts
│   ├── repository-overview.md   # This file
│   ├── repository-structure.md  # Source tree layout (this file)
│   ├── data-model.md            # Theme descriptor and asset model
│   ├── components/
│   │   └── theme-package.md     # Theme package deep dive
│   ├── integrations.md          # GRUB2, grub-mkconfig, packaging
│   ├── build-and-deployment.md  # Installation and deployment
│   ├── compatibility-contracts.md # Format, path, version contracts
│   ├── testing.md               # Verification procedures
│   ├── troubleshooting.md       # Common issues and solutions
│   ├── change-guide.md          # Safe modification procedures
│   ├── documentation-maintenance.md # Doc ownership and updates
│   └── faq.md                   # Frequently asked questions
├── Nuci/                        # Theme package (deployment unit)
│   ├── theme.txt                # Theme descriptor (authoritative)
│   ├── background.png           # Boot menu background
│   ├── select_c.png             # Selection sprite: centre
│   ├── select_n.png             # Selection sprite: north
│   ├── select_ne.png            # Selection sprite: north-east
│   ├── select_e.png             # Selection sprite: east
│   ├── select_se.png            # Selection sprite: south-east
│   ├── select_s.png             # Selection sprite: south
│   ├── select_sw.png            # Selection sprite: south-west
│   ├── select_w.png             # Selection sprite: west
│   ├── terminal_box_c.png       # Terminal box: centre
│   ├── terminal_box_n.png       # Terminal box: north
│   ├── terminal_box_ne.png      # Terminal box: north-east
│   ├── terminal_box_e.png       # Terminal box: east
│   ├── terminal_box_se.png      # Terminal box: south-east
│   ├── terminal_box_s.png       # Terminal box: south
│   ├── terminal_box_sw.png      # Terminal box: south-west
│   ├── terminal_box_w.png       # Terminal box: west
│   ├── terminal_box_nw.png      # Terminal box: north-west
│   └── icons/                   # Boot entry class icons
│       ├── archlinux.png
│       ├── debian.png
│       ├── fedora.png
│       ├── ubuntu.png
│       └── ... (other icons)
├── ARCHITECTURE.md              # Root architecture document
├── CODE_OF_CONDUCT.md           # Community conduct standards
├── CONTRIBUTING.md              # Contribution guidelines
├── LICENSE                      # GPL-3.0-or-later licence
├── PRIVACY.md                   # Privacy statement
├── README.md                    # Project overview and installation
└── SECURITY.md                  # Security policy
```

## Module Organisation

This repository has **no code modules**. The only organisational unit is the **theme package** (`Nuci/`), which is a self-contained directory deployed as a unit.

### Theme Package (`Nuci/`)

| Path | Role | Description |
|------|------|-------------|
| `theme.txt` | Descriptor | Single source of truth for theme parameters; parsed by GRUB2 |
| `background.png` | Asset | Boot menu background image |
| `select_*.png` | Assets (8) | Directional selection highlight sprites |
| `terminal_box_*.png` | Assets (9) | 9-slice terminal box sprites |
| `icons/` | Directory | Distribution/class icons for boot entries |

## Asset Catalogue

### Theme Descriptor
- **File:** `Nuci/theme.txt`
- **Format:** GRUB2 theme specification (key-value pairs, named blocks)
- **Encoding:** UTF-8
- **Size:** ~1 KB

### Raster Assets (All PNG)

| Category | Count | Naming Pattern | Purpose |
|----------|-------|----------------|---------|
| Background | 1 | `background.png` | Boot menu backdrop |
| Selection sprites | 8 | `select_{c,n,ne,e,se,s,sw,w}.png` | Menu item highlight |
| Terminal box sprites | 9 | `terminal_box_{c,n,ne,e,se,s,sw,w,nw}.png` | 9-slice terminal frame |
| Icons | ~20+ | `icons/{distro}.png` | Boot entry class icons |

### Icon Inventory (Representative)

The `icons/` directory contains distribution-specific icons. Common entries include:
- `archlinux.png`, `debian.png`, `fedora.png`, `ubuntu.png`
- `linuxmint.png`, `opensuse.png`, `manjaro.png`, `gentoo.png`
- `windows.png`, `uefi.png`, `memtest.png`, `shutdown.png`, `reboot.png`
- Generic fallbacks: `linux.png`, `unknown.png`

Exact inventory varies; see `ls Nuci/icons/` for current list.

## Community Health Files (Root)

| File | Purpose | Standard Location |
|------|---------|-------------------|
| `README.md` | Project overview, installation, links | Root (GitHub default) |
| `CONTRIBUTING.md` | Contribution guidelines | Root (GitHub default) |
| `SECURITY.md` | Security policy, vulnerability reporting | Root (GitHub default) |
| `PRIVACY.md` | Privacy and data handling | Root (project convention) |
| `CODE_OF_CONDUCT.md` | Community conduct standards | Root (GitHub default) |
| `ARCHITECTURE.md` | Architecture documentation | Root (project convention) |
| `LICENSE` | Licence terms (GPL-3.0-or-later) | Root (standard) |
| `.github/FUNDING.yml` | Funding platform links | `.github/` (GitHub default) |

## Documentation Directory (`docs/`)

Generated documentation complementing root files. See [INDEX.md](./INDEX.md) for catalogue.

### Structure Principles

- **No duplication** of root documents (`ARCHITECTURE.md`, `SECURITY.md`, etc.)
- **Complementary elaboration** only (e.g., `docs/architecture.md` expands on root `ARCHITECTURE.md`)
- **Kebab-case filenames** (`data-model.md`, not `dataModel.md`)
- **Relative links** from `docs/` root (`../ARCHITECTURE.md`, `./components/theme-package.md`)

## Deployment Unit

The **entire `Nuci/` directory** is the atomic deployment unit. It is copied to `/usr/share/grub/themes/Nuci/` preserving internal structure.

No other files from the repository are installed to the system.