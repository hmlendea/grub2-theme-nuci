# Design Decisions — GRUB2 Theme Nuci

This document records key architectural and design choices with their rationale.

## 1. Static Asset Package Model

**Decision:** The repository is a pure static asset package with no build pipeline, no code generation, no templating, and no executable code.

**Rationale:**
- GRUB2 themes are inherently static data consumed at boot time
- Eliminates build infrastructure, CI complexity, and dependency management
- Ensures reproducibility: what is in the repository is exactly what is installed
- Simplifies contribution: contributors edit assets directly
- Aligns with distribution packaging expectations (simple file copy)

**Consequences:**
- No automated asset validation (manual verification required)
- No asset optimisation pipeline (contributors must provide correctly-sized assets)
- No templating for resolution variants (single asset set serves all resolutions)

## 2. PNG-Only Raster Format

**Decision:** All raster assets use PNG format exclusively.

**Rationale:**
- GRUB2's theme engine natively supports PNG
- Lossless compression preserves quality for UI elements
- Transparency support required for selection sprites and terminal box sprites
- Universal tool support for editing

**Consequences:**
- No JPEG, WebP, or SVG assets permitted
- No vector source assets maintained (contributors provide final PNGs)
- File sizes larger than lossy formats but acceptable for boot assets

## 3. Single Directory Deployment

**Decision:** The entire theme resides in one directory (`Nuci/`) deployed to `/usr/share/grub/themes/Nuci/`.

**Rationale:**
- GRUB2 expects theme assets relative to `theme.txt` location
- FHS-compliant location for GRUB themes
- Atomic deployment: single `cp -r` operation
- No scattered files across filesystem

**Consequences:**
- Relocation requires `GRUB_THEME` update and `grub-mkconfig` re-run
- No support for shared assets across multiple themes
- All assets must be self-contained

## 4. GRUB2 Theme Specification Compliance

**Decision:** The theme descriptor (`theme.txt`) and asset layout strictly follow the GRUB2 theme specification.

**Rationale:**
- Ensures compatibility with target GRUB2 versions
- Leverages GRUB2's built-in theming engine (9-slice scaling, sprite patterns)
- No custom parsing or rendering code required

**Consequences:**
- Limited to GRUB2's theme capabilities (no animations, no scripting)
- Must track GRUB2 upstream changes for compatibility
- Theme expressiveness bounded by specification

## 5. Glob Patterns for Asset References

**Decision:** `theme.txt` uses glob patterns (`select_*.png`, `terminal_box_*.png`) for sprite collections.

**Rationale:**
- Reduces maintenance: adding a sprite only requires adding the file
- Declarative: GRUB2 resolves patterns at runtime
- Matches GRUB2's documented pattern syntax

**Consequences:**
- Filename discipline required: all sprites must match pattern
- No explicit asset manifest; discovery is implicit
- Pattern changes require GRUB2 version compatibility check

## 6. No Runtime Configuration

**Decision:** The theme cannot adapt to screen resolution, DPI, firmware type (BIOS/UEFI), or user preferences at boot time.

**Rationale:**
- GRUB2 theme engine has no runtime configuration mechanism
- Static assets are simpler and more reliable
- Boot-time constraints (memory, speed) favour static approach

**Consequences:**
- Single asset set must work across resolutions (relies on GRUB2 scaling)
- No dark/light mode, no accessibility variants
- Customisation requires editing `theme.txt` and regenerating config

## 7. Community Health Files at Repository Root

**Decision:** `CONTRIBUTING.md`, `SECURITY.md`, `PRIVACY.md`, `CODE_OF_CONDUCT.md`, `ARCHITECTURE.md`, `LICENSE` reside at repository root.

**Rationale:**
- GitHub and tooling expect these at root
- Discoverable by contributors, security researchers, users
- Standard open-source practice

**Consequences:**
- Documentation under `docs/` complements but does not duplicate root files
- Cross-references use relative links from `docs/` to root

## 8. British English Spelling

**Decision:** All documentation uses British spelling (organise, realise, licence, behaviour, etc.).

**Rationale:**
- Consistent with maintainer preference and project conventions
- Aligns with `language.english.instructions.md` guidance

**Consequences:**
- Contributors should follow British spelling in documentation
- Code comments and strings follow same convention