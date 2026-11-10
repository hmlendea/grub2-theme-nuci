# GRUB2 Theme Nuci — Documentation Index

**Repository:** `grub2-theme-nuci`
**Purpose:** A static GRUB2 theme package providing a Vimix-inspired boot menu theme with complete theme definition and icon assets.
**Scope:** Theme descriptor (`Nuci/theme.txt`), raster assets (PNG), installation documentation, and community health files. No executable code, build pipeline, or runtime process.

---

## Root Document Map

| Document | Location | Scope |
|----------|----------|-------|
| Architecture | [ARCHITECTURE.md](../ARCHITECTURE.md) | System context, components, runtime flows, dependencies, constraints |
| Security Policy | [SECURITY.md](../SECURITY.md) | Vulnerability reporting, supported versions, disclosure policy |
| Roadmap | [ROADMAP.md](../ROADMAP.md) | Planned work and forthcoming changes (if exists) |
| Privacy Statement | [PRIVACY.md](../PRIVACY.md) | Data handling, privacy guarantees |
| Licence | [LICENSE](../LICENSE) | GPL-3.0-or-later terms |
| Contributing | [CONTRIBUTING.md](../CONTRIBUTING.md) | Contribution guidelines, issue reporting, PR process |
| Code of Conduct | [CODE_OF_CONDUCT.md](../CODE_OF_CONDUCT.md) | Community conduct standards |

---

## Documentation Catalogue

### Architecture & Design
| File | Description |
|------|-------------|
| [architecture.md](./architecture.md) | High-level architecture complementing root ARCHITECTURE.md; elaborates on theme package structure, GRUB2 integration boundary, and asset contracts |
| [design-decisions.md](./design-decisions.md) | Key design choices: static package model, PNG-only assets, single-directory deployment, GRUB2 spec compliance |
| [invariants.md](./invariants.md) | System-wide invariants: asset reference integrity, installation path stability, GRUB2 spec conformance |

### Repository Structure
| File | Description |
|------|-------------|
| [repository-overview.md](./repository-overview.md) | Repository purpose, scope, entry points, and principal capabilities |
| [repository-structure.md](./repository-structure.md) | Source tree layout, module organisation, asset catalogue |

### Theme Specification & Assets
| File | Description |
|------|-------------|
| [data-model.md](./data-model.md) | Theme descriptor format, asset declarations, layout parameters, colour and font specifications |
| [components/theme-package.md](./components/theme-package.md) | Deep dive on the Nuci theme package: descriptor, background, selection sprites, terminal box sprites, icons |

### Integration & Deployment
| File | Description |
|------|-------------|
| [integrations.md](./integrations.md) | GRUB2 bootloader integration, grub-mkconfig generation, distribution packaging (AUR), system configuration |
| [build-and-deployment.md](./build-and-deployment.md) | Installation procedure, deployment unit, filesystem requirements, packaging scripts |
| [compatibility-contracts.md](./compatibility-contracts.md) | Theme.txt format stability, asset filename contracts, installation path invariants, GRUB2 version compatibility |

### Operations & Verification
| File | Description |
|------|-------------|
| [testing.md](./testing.md) | Manual verification procedure, coverage gaps, multi-resolution considerations |
| [troubleshooting.md](./troubleshooting.md) | Common installation issues, render artifacts, missing assets, fallback behaviour |

### Community & Maintenance
| File | Description |
|------|-------------|
| [change-guide.md](./change-guide.md) | How to safely modify theme assets, update descriptor, add icons, test changes |
| [documentation-maintenance.md](./documentation-maintenance.md) | Documentation ownership, review cadence, update triggers |
| [faq.md](./faq.md) | Frequently asked questions about installation, customisation, compatibility |

---

## Navigation Aids

| Audience | Start Here |
|----------|------------|
| Newcomers | [quick-start.md](./quick-start.md) → [repository-overview.md](./repository-overview.md) → [architecture.md](./architecture.md) |
| Theme Customisers | [components/theme-package.md](./components/theme-package.md) → [data-model.md](./data-model.md) → [change-guide.md](./change-guide.md) |
| Packagers / Distributors | [integrations.md](./integrations.md) → [build-and-deployment.md](./build-and-deployment.md) → [compatibility-contracts.md](./compatibility-contracts.md) |
| Debuggers | [troubleshooting.md](./troubleshooting.md) → [testing.md](./testing.md) → [integrations.md](./integrations.md) |

---

## Maintenance Metadata

- **Last reviewed:** 2026-11-10
- **Owner:** Repository maintainers
- **Coverage status:** Complete for current repository scope (static theme package)
- **Next review trigger:** New GRUB2 theme specification changes, asset additions, or packaging workflow modifications