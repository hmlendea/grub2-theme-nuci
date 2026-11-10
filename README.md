[![Donate](https://img.shields.io/badge/-%E2%99%A5%20Donate-%23ff69b4)](https://hmlendea.go.ro/fund.html)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://gnu.org/licenses/gpl-3.0)

# GRUB2 Theme Nuci

A simple theme for GRUB2 based on the Vimix aesthetic. The repository includes the theme definition and the icon assets required to apply the `Nuci` theme.

## 📑 Table of Contents

- [Features](#-features)
- [Installation](#-installation)
- [Contributing](#-contributing)
- [Support](#-support)
- [Privacy and Data](#-privacy-and-data)
- [Security](#-security)
- [Code of Conduct](#-code-of-conduct)
- [Architecture](#-architecture)
- [Documentation](#-documentation)
- [License](#-license)

## ✨ Features

- Vimix-inspired GRUB2 theme styling
- Complete theme definition in `Nuci/theme.txt`
- Included icon assets under `Nuci/icons/`
- Manual installation or distribution via AUR/PKGBUILD

## 📦 Installation

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

## 🤝 Contributing

You are welcome to submit any suggestion, feedback, or modification to this project.

When doing so, please:
- Maintain cross-platform compatibility
- Submit focused pull requests that conform to the existing code style
- Maintain your branch synchronised with `master`
- Revise the documentation when functionality changes
- Properly test all modifications, including edge cases and error conditions

See the [Contributing Guidelines](./CONTRIBUTING.md) for details on how to report issues, suggest enhancements, and submit changes.

## 💝 Support

Discovered a problem or have a suggestion? [Open an issue](https://github.com/hmlendea/grub2-theme-nuci/issues)!

If you find this project useful, consider [funding it](https://hmlendea.go.ro/funding) or starring ⭐️ it on GitHub!

[![Donate](https://raw.githubusercontent.com/hmlendea/readme-assets/master/donate_generic.png)](https://hmlendea.go.ro/funding)

## 🛡️ Privacy and Data

GRUB2 Theme Nuci is a static theme package with no runtime logic, data collection, or telemetry.

For the detailed description of how the application handles privacy and personal data, see the [Privacy Statement](./PRIVACY.md).

## 🔒 Security

GRUB2 Theme Nuci is a static theme package with no executable code, runtime logic, dependencies, or network connectivity.

For information on reporting security vulnerabilities, see the [Security Policy](./SECURITY.md).

## 📜 Code of Conduct

See the [Code of Conduct](./CODE_OF_CONDUCT.md) for the standards of conduct expected in all project spaces.

## 🏗️ Architecture

GRUB2 Theme Nuci is a static theme package with no executable code, build pipeline, or runtime process.

See the [Architecture Document](./ARCHITECTURE.md) for the system context, principal components, runtime flows, ownership boundaries, dependencies, constraints, and extension points.

## � Documentation

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

## 📄 License

Licensed under the `GPL-3.0-or-later`.
See [LICENSE](./LICENSE) for details.
