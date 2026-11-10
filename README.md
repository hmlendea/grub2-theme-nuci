[![Donate](https://img.shields.io/badge/-%E2%99%A5%20Donate-%23ff69b4)](https://hmlendea.go.ro/fund.html)
[![License: GPL v3](https://img.shields.io/badge/License-GPLv3-blue.svg)](https://gnu.org/licenses/gpl-3.0)

# GRUB2 Theme Nuci

A simple theme for GRUB2 based on the Vimix aesthetic. The repository includes the theme definition and the icon assets required to apply the `Nuci` theme.

## 📑 Table of Contents

- [Features](#-features)
- [Installation](#-installation)
- [Contributing](#-contributing)
- [Support](#-support)
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

## 📄 License

Licensed under the `GPL-3.0-or-later`.
See [LICENSE](./LICENSE) for details.
