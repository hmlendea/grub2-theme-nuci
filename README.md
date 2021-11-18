[![Latest GitHub release](https://img.shields.io/github/v/release/hmlendea/grub2-theme-nuci)](https://github.com/hmlendea/grub2-theme-nuci/releases/latest)

# About

Personal GRUB2 theme based on Vimix.

# Installation

## PKGBUILD

Install it using [this PKGBUILD](https://github.com/hmlendea/PKGBUILDs/tree/master/pkg/grub2-theme-nuci).

## Manual

Copy the `Nuci` directory into `/usr/share/grub/themes` and make it executable:
```bash
cp -r "Nuci" "/usr/share/grub/themes/"
```

Set the GRUB_THEME setting in `/etc/default/grub`:
```bash
...
GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt
...
```

Update your GRUB configuration using `grub-mkconfig` or `update-grub`
