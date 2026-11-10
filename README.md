[![Donate](https://img.shields.io/badge/-%E2%99%A5%20Donate-%23ff69b4)](https://hmlendea.go.ro/funding)
[![License](https://img.shields.io/github/license/hmlendea/grub2-theme-nuci)](https://github.com/hmlendea/grub2-theme-nuci/blob/master/LICENSE)

# GRUB2 Theme Nuci

A static GRUB2 theme package providing a Vimix-inspired visual style for the GRUB bootloader. The repository contains the theme definition (`Nuci/theme.txt`) and all required icon assets (`Nuci/icons/`).

## 📑 Table of Contents

- [Capabilities](#-capabilities)
- [Usage](#-usage)
- [Installation](#-installation)
- [Compatibility](#-compatibility)
- [Architecture](#-architecture)
- [Documentation](#-documentation)
- [Contributing](#-contributing)
- [Code of Conduct](#-code-of-conduct)
- [Security](#-security)
- [Privacy and Data](#-privacy-and-data)
- [Project Engagement](#-project-engagement)
- [License](#-license)

## ✨ Capabilities

- Vimix-inspired GRUB2 theme styling
- Complete theme definition in `Nuci/theme.txt`
- Included icon assets under `Nuci/icons/`
- Manual installation or distribution via AUR/PKGBUILD

## 🚀 Usage

Install the theme by copying the `Nuci` directory into the GRUB themes folder and then configure GRUB to use it.

```bash
sudo cp -r "Nuci" "/usr/share/grub/themes/"
sudo chmod -R 755 "/usr/share/grub/themes/Nuci"
```

Edit `/etc/default/grub` and set:

```bash
GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt
```

Regenerate the boot configuration:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## 📦 Installation

[![Obtain it from AUR](https://raw.githubusercontent.com/hmlendea/readme-assets/master/install_from_aur.png)](https://aur.archlinux.org/packages/grub2-theme-nuci)

### Package Manager Installation

```bash
paru -S grub2-theme-nuci
```

or, if you use `yay`:

```bash
yay -S grub2-theme-nuci
```

### Manual Installation

```bash
sudo cp -r "Nuci" "/usr/share/grub/themes/"
sudo chmod -R 755 "/usr/share/grub/themes/Nuci"
```

Edit `/etc/default/grub` and set:

```bash
GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt
```

Regenerate the boot configuration:

```bash
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## 🧩 Compatibility

| Component | Supported Versions | Notes |
|-----------|--------------------|-------|
| GRUB2 | 2.04+ | Tested with GRUB 2.06 and 2.12 |

## 🏗️ Architecture

See the [Architecture Documentation](./ARCHITECTURE.md) for the system context, principal components, runtime flows, ownership boundaries, dependencies, constraints, and extension points.

## 📚 Documentation

Comprehensive documentation is available in the [`docs/`](./docs/) directory:

| Category | Documents |
|----------|-----------|
| **Getting Started** | [Quick Start](./docs/quick-start.md) |
| **Architecture & Design** | [Architecture](./docs/architecture.md) • [Design Decisions](./docs/design-decisions.md) • [Invariants](./docs/invariants.md) |
| **Repository Structure** | [Overview](./docs/repository-overview.md) • [Structure](./docs/repository-structure.md) |
| **Theme Specification** | [Data Model](./docs/data-model.md) • [Theme Package](./docs/components/theme-package.md) |
| **Integration & Deployment** | [Integrations](./docs/integrations.md) • [Build & Deployment](./docs/build-and-deployment.md) • [Compatibility Contracts](./docs/compatibility-contracts.md) |
| **Operations** | [Testing](./docs/testing.md) • [Troubleshooting](./docs/troubleshooting.md) |
| **Community & Maintenance** | [Change Guide](./docs/change-guide.md) • [Documentation Maintenance](./docs/documentation-maintenance.md) • [FAQ](./docs/faq.md) |
| **API Reference** | [Index](./docs/api-reference/INDEX.md) • [Theme Descriptor](./docs/api-reference/theme-descriptor.md) • [Asset Specifications](./docs/api-reference/asset-specifications.md) • [Icon Mapping](./docs/api-reference/icon-mapping.md) |
| **Behaviour** | [Boot Sequence](./docs/behaviour/boot-sequence.md) • [Rendering](./docs/behaviour/rendering.md) • [Input Handling](./docs/behaviour/input-handling.md) |
| **Flows** | [Theme Load](./docs/flows/theme-load.md) • [Asset Load](./docs/flows/asset-load.md) • [Menu Render](./docs/flows/menu-render.md) |
| **Additional** | [API Usage Examples](./docs/api-usage-examples.md) • [Ambiguities & Open Questions](./docs/ambiguities-and-open-questions.md) • [Configuration](./docs/configuration.md) • [Concurrency & Scheduling](./docs/concurrency-and-scheduling.md) • [Dependencies](./docs/dependencies.md) • [Error Handling](./docs/error-handling.md) • [Logging](./docs/logging.md) • [Security](./docs/security.md) • [State & Persistence](./docs/state-and-persistence.md) |

See the [Documentation Index](./docs/INDEX.md) for the complete catalogue with descriptions.

## 🤝 Contributing

You are welcome to submit any suggestion, feedback, or modification to this project.

When doing so, please:
- Maintain cross-platform compatibility
- Submit focused pull requests that conform to the existing code style
- Maintain your branch synchronised with `master`
- Revise the documentation when functionality changes
- Properly test all modifications, including edge cases and error conditions

See the [Contributing Guidelines](./CONTRIBUTING.md) for details on how to report issues, suggest enhancements, and submit changes.

## 📜 Code of Conduct

See the [Code of Conduct](./CODE_OF_CONDUCT.md) for the standards of conduct expected in all project spaces.

## 🔒 Security

GRUB2 Theme Nuci is a static theme package with no executable code, runtime logic, dependencies, or network connectivity.

For information on reporting security vulnerabilities, see the [Security Policy](./SECURITY.md).

## 🛡️ Privacy and Data

GRUB2 Theme Nuci is a static theme package with no runtime logic, data collection, or telemetry.

For the detailed description of how the application handles privacy and personal data, see the [Privacy Statement](./PRIVACY.md).

## 💝 Project Engagement

Discovered a problem or have a suggestion? [Open an issue](https://github.com/hmlendea/grub2-theme-nuci/issues)!

If you find this project useful, consider [funding it](https://hmlendea.go.ro/funding) or starring ⭐️ it on GitHub!

[![Donate](https://raw.githubusercontent.com/hmlendea/readme-assets/master/donate_generic.png)](https://hmlendea.go.ro/funding)

## 📄 License

This project is being distributed under the `GPL-3.0-or-later`.
See [LICENSE](./LICENSE) for additional information.
