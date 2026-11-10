# Frequently Asked Questions — GRUB2 Theme Nuci

## General

### What is GRUB2 Theme Nuci?

A static GRUB2 boot menu theme package. It contains a theme descriptor (`theme.txt`), raster assets (background, selection sprites, terminal box sprites), and distribution icons. No executable code, no build pipeline, no runtime logic.

### What does "Nuci" mean?

"Nuci" is Romanian for "walnuts". The theme name follows the maintainer's naming convention.

### Is this affiliated with GRUB2?

No. This is a community theme package that conforms to the GRUB2 theme specification. GRUB2 is developed by the GNU Project. This theme is consumed by GRUB2 at boot time but is not part of GRUB2 itself.

## Installation

### How do I install the theme?

**Manual:**
```bash
sudo cp -r Nuci /usr/share/grub/themes/
sudo chmod -R 755 /usr/share/grub/themes/Nuci
sudo find /usr/share/grub/themes/Nuci -type f -exec chmod 644 {} \;
sudo sed -i 's|^#\?GRUB_THEME=.*|GRUB_THEME=/usr/share/grub/themes/Nuci/theme.txt|' /etc/default/grub
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

**AUR (Arch Linux):** Install `grub2-theme-nuci` from the Arch User Repository.

See [build-and-deployment.md](./build-and-deployment.md) for full details.

### Where must the theme be installed?

`/usr/share/grub/themes/Nuci/` — the standard GRUB2 themes directory per FHS. The path must contain `theme.txt` at its root.

### Why isn't the theme showing after installation?

Most common causes:
1. `GRUB_THEME` not set in `/etc/default/grub`
2. `grub-mkconfig` not re-run after setting `GRUB_THEME`
3. Theme files not at expected path
4. File permissions incorrect

See [troubleshooting.md](./troubleshooting.md) for diagnosis.

### Does the theme work on both BIOS and UEFI systems?

Yes. The theme is firmware-agnostic. GRUB2 loads it regardless of firmware mode. However, rendering may differ slightly between BIOS (VBE) and UEFI (GOP) video modes. Test both if possible.

## Usage

### How do I change the boot menu appearance?

Edit `Nuci/theme.txt`:
- **Colours:** Modify `color_normal`, `color_highlight`, etc.
- **Layout:** Modify `left`, `top`, `width`, `height` percentages
- **Font:** Modify `font` property
- **Assets:** Replace PNG files (keep same filenames)

After editing, reinstall and re-run `grub-mkconfig`.

### How do I add an icon for my distribution?

1. Create `{class}.png` (e.g., `manjaro.png`)
2. Place in `Nuci/icons/`
3. No `theme.txt` change needed — GRUB2 auto-discovers icons by filename

The icon filename must match the boot entry's `class` field exactly (case-sensitive).

### How do I change the boot menu position or size?

Edit the `boot_menu` block in `theme.txt`:
```
+ boot_menu {
    left = 36%
    top = 30%
    width = 28%
    height = 60%
    ...
}
```

Values are percentages of screen dimensions. Keep within 0-100%.

### How do I disable the timeout countdown display?

Remove or comment out the `timeout` block in `theme.txt`, or set `visible = false` if supported by your GRUB2 version.

### How do I access the GRUB2 command line?

Press `c` at the boot menu. The terminal box sprites (`terminal_box_*.png`) frame the command line interface.

## Assets

### Why PNG only? Why not JPEG or SVG?

- **PNG:** Lossless, supports transparency, universally supported by GRUB2
- **JPEG:** Lossy compression artifacts; no transparency; not ideal for UI elements
- **SVG:** GRUB2 does not natively render SVG; would require rasterisation at build time

### Why are there 8 selection sprites?

GRUB2's selection widget renders as an 8-directional border (like a 9-slice without center). The 8 sprites are: `c` (center), `n`, `ne`, `e`, `se`, `s`, `sw`, `w`. Each renders one edge/corner of the selection highlight.

### Why are there 9 terminal box sprites?

GRUB2's terminal box uses 9-slice scaling: 4 corners (`nw`, `ne`, `sw`, `se`), 4 edges (`n`, `e`, `s`, `w`), and 1 center (`c`). This allows the box to scale to any size while preserving corner proportions.

### Can I use higher resolution assets?

Yes, but test thoroughly. GRUB2 scales assets to fit. Very large assets increase boot time (decoding overhead). Very small assets may appear blurry when upscaled. Match target display resolutions.

### Can I add animated elements?

GRUB2's theme engine has limited animation support (e.g., `animation` property for some widgets). The Nuci theme does not use animation. Adding animation requires GRUB2 version support and careful testing.

## Development

### How do I contribute?

See [CONTRIBUTING.md](../CONTRIBUTING.md). Contributions are accepted via GitHub pull requests targeting the `master` branch.

### How do I test my changes?

1. Run asset integrity checks (see [testing.md](./testing.md))
2. Install to a test system or VM
3. Reboot and visually verify all elements
4. Test on multiple resolutions and firmware modes

There is no automated test suite; verification is manual and visual.

### Can I fork and redistribute?

Yes. The theme is licensed under GPL-3.0-or-later. Forking and redistribution are permitted under the licence terms. See [LICENSE](../LICENSE).

### Why are there no unit tests?

The repository contains no executable code — only static assets consumed by GRUB2 at boot time. There are no functions, classes, or modules to unit test. Verification is integration-level (boot test) and manual.

## Licensing

### What licence is the theme under?

GNU General Public License v3.0 or later (GPL-3.0-or-later). See [LICENSE](../LICENSE).

### Can I use the assets in my own theme?

Yes, under GPL-3.0-or-later terms. Your derivative work must also be licensed under GPL-3.0-or-later (or compatible). Attribute the original source.

### Can I use the theme commercially?

GPL-3.0-or-later permits commercial use, modification, and distribution, provided the licence terms are met (source availability, same licence for derivatives, etc.).

## Security

### Does the theme collect any data?

No. The theme is a static asset package with no runtime logic, no network access, no telemetry, and no data collection. See [PRIVACY.md](../PRIVACY.md).

### How do I report a security issue?

See [SECURITY.md](../SECURITY.md). Security vulnerabilities may be reported via GitHub Security Advisories or direct maintainer contact.

### Can a malicious theme harm my system?

A theme is consumed by GRUB2 at boot time. A malicious theme could potentially exploit GRUB2 vulnerabilities (buffer overflows in asset parsing, etc.). Only install themes from trusted sources. This theme's assets are simple PNGs with no embedded scripts.

## Maintenance

### How often is the theme updated?

On an as-needed basis. Updates typically include: new distribution icons, visual refinements, compatibility fixes. Check GitHub Releases for version history.

### How do I update the theme?

**Manual:** Reinstall the updated `Nuci/` directory and re-run `grub-mkconfig`.

**AUR:** Update the package via your AUR helper.

### Will updating break my customisations?

If you modified installed files directly, yes — updates overwrite them. To preserve customisations:
1. Fork the repository
2. Apply customisations to your fork
3. Install from your fork

Or maintain a separate theme directory with your modifications.