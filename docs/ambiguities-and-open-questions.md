# Ambiguities and Open Questions — GRUB2 Theme Nuci

Documents unresolved design decisions, specification gaps, and areas requiring clarification.

## Theme Specification Ambiguities

### 1. GRUB2 Theme Spec Completeness

**Issue:** The GRUB2 theme specification is not formally documented as a standalone document. It is derived from:
- GRUB2 source code (`grub-core/normal/theme.c`, `grub-core/normal/menu.c`)
- Example themes in GRUB2 source tree
- Community documentation (Arch Wiki, etc.)

**Impact:** Uncertainty about:
- Complete list of supported properties and blocks
- Behaviour of edge cases (percentages > 100%, negative values)
- Animation support details
- Font fallback chain
- Video mode change handling

**Resolution Needed:** Formal specification or upstream documentation contribution.

### 2. Percentage Value Semantics

**Issue:** Percentage values (0-100%) for layout properties lack precise definition:
- Is 100% exactly screen edge or inside safe area?
- How are fractional percentages handled?
- Do percentages apply to viewport or framebuffer?

**Current Assumption:** 0% = left/top edge, 100% = right/bottom edge of framebuffer.

**Verification Needed:** Test at multiple resolutions and firmware modes.

### 3. Sprite Dimension Requirements

**Issue:** No documented required dimensions for:
- Selection sprites (`select_*.png`)
- Terminal box sprites (`terminal_box_*.png`)

**Current Practice:** Derived from existing themes and trial/error:
- Selection: height = `item_height` (32px), width variable
- Terminal box: 9-slice compatible dimensions

**Risk:** GRUB2 version changes could break assumptions.

### 4. Icon Discovery Algorithm

**Issue:** Exact icon matching algorithm not specified:
- Case sensitivity (observed: case-sensitive)
- Multiple `--class` values: which takes priority?
- Fallback chain order (observed: exact → linux → unknown)

**Impact:** New distributions may not get icons if class naming differs.

### 5. Font Specification Format

**Issue:** Font string format `"Family Style Size"` parsing rules unclear:
- Style tokens: `Regular`, `Bold`, `Italic`, `Bold Italic` — others?
- Size units: points? pixels? GRUB2 internal units?
- Family matching: exact? fuzzy? fallback?

**Current Practice:** `"Unifont Regular 16"` works; others untested.

## Asset Pipeline Ambiguities

### 6. PNG Feature Support

**Issue:** Which PNG features does GRUB2's PNG decoder support?
- Interlacing (observed: non-interlaced only)
- Colour profiles (sRGB chunk)
- Gamma correction
- Transparency types (tRNS vs alpha channel)
- Bit depths (8-bit confirmed; 16-bit?)

**Risk:** Assets with unsupported features may fail silently.

### 7. Asset Loading Order

**Issue:** No guaranteed load order for assets. Current observation:
1. Background
2. Selection sprites
3. Terminal box sprites
4. Icons (lazy)

**Impact:** Memory pressure during boot; no way to prioritise.

### 8. Video Memory Limits

**Issue:** No documented limit on video memory for theme assets.
- Maximum texture size?
- Total asset memory budget?
- Behaviour on OOM?

**Risk:** Large background + many icons could exhaust video memory on constrained firmware.

## Behavioural Ambiguities

### 9. Theme Reload on Video Mode Change

**Issue:** What happens when video mode changes after theme load?
- Observed: Theme re-renders at new resolution
- Assets: Re-scaled or re-decoded?
- State: Selection position preserved?

**Testing Needed:** GRUB2 command line `videomode` command during theme.

### 10. Submenu Theme Inheritance

**Issue:** When entering a submenu, does theme persist?
- Observed: Yes, same theme
- But: Layout recalculated? Assets reloaded?

### 11. Serial Console Interaction

**Issue:** Theme renders to framebuffer; serial console sees text only.
- Can theme detect serial console?
- Should theme disable graphics for serial?
- Current: No detection; graphics always rendered.

## Distribution Integration Ambiguities

### 12. AUR Package Versioning

**Issue:** No formal version scheme for theme releases.
- Git tags used informally
- No semantic versioning
- AUR package version decoupled from theme changes

**Resolution Needed:** Define versioning policy.

### 13. Distribution-Specific GRUB2 Patches

**Issue:** Some distributions patch GRUB2 (e.g., Ubuntu, Fedora).
- Do patches affect theme engine?
- Theme tested on upstream GRUB2 only
- Distribution-specific behaviour possible

**Mitigation:** Test on target distributions.

### 14. Secure Boot Interaction

**Issue:** Theme assets loaded before kernel; Secure Boot verifies kernel only.
- Theme not signed/verified
- Malicious theme could exploit GRUB2 parser
- No mitigation in theme package

**Documentation Needed:** Security considerations for theme consumers.

## Open Questions for Maintainers

### 15. Animation Support

**Question:** Should the theme use GRUB2's animation properties?
- `animation` property exists for some widgets
- Could animate selection highlight, timeout label
- Adds complexity; minimal visual benefit

**Decision Pending:** Cost/benefit analysis.

### 16. High-DPI / Retina Support

**Question:** How to handle high-DPI displays?
- GRUB2 video mode in pixels
- Theme percentages scale automatically
- But: pixel-perfect sprites may appear small
- Solution: Provide @2x assets? GRUB2 support?

**Research Needed:** GRUB2 HiDPI capabilities.

### 17. Right-to-Left Language Support

**Question:** Does theme support RTL boot entries?
- GRUB2 text rendering: LTR only (observed)
- Menu layout: left-aligned percentages
- Icons: left of text
- No RTL awareness in theme

**Decision:** Document limitation; no action unless requested.

### 18. Accessibility Compliance

**Question:** Should theme meet accessibility standards?
- WCAG contrast ratios for colours
- Font size minimums
- Focus indicator visibility
- Current: Configurable but defaults may not meet standards

**Action:** Document current state; consider high-contrast variant.

### 19. Theme Variants

**Question:** Provide multiple theme variants (dark/light, compact/spacious)?
- Current: Single theme
- Variants would require multiple theme.txt files
- Installation complexity increases

**Decision:** Not planned; users can fork and customise.

### 20. Automated Testing

**Question:** Invest in automated visual regression testing?
- QEMU + screenshot comparison
- High effort for static asset package
- Manual testing currently sufficient

**Decision:** Defer until regression risk increases.

## Tracking

| ID | Topic | Priority | Status |
|----|-------|----------|--------|
| AMB-01 | Theme spec completeness | High | Open |
| AMB-02 | Percentage semantics | Medium | Assumed |
| AMB-03 | Sprite dimensions | Medium | Empirical |
| AMB-04 | Icon discovery | Medium | Observed |
| AMB-05 | Font format | Low | Working |
| AMB-06 | PNG features | Medium | Partial |
| AMB-07 | Load order | Low | Observed |
| AMB-08 | Video memory | Low | Unknown |
| AMB-09 | Mode change reload | Medium | Partial |
| AMB-10 | Submenu inheritance | Low | Assumed |
| AMB-11 | Serial console | Low | Known gap |
| AMB-12 | AUR versioning | Medium | Open |
| AMB-13 | Distro patches | Medium | Untested |
| AMB-14 | Secure Boot | High | Documented |
| OPQ-15 | Animation | Low | Deferred |
| OPQ-16 | HiDPI | Medium | Research |
| OPQ-17 | RTL | Low | Documented |
| OPQ-18 | Accessibility | Medium | Partial |
| OPQ-19 | Variants | Low | Deferred |
| OPQ-20 | Automated testing | Low | Deferred |

## Resolution Process

For each ambiguity/open question:
1. **Research:** Test behaviour, consult GRUB2 source, search community
2. **Document:** Record findings in this file
3. **Decide:** Accept, workaround, or fix
4. **Update:** Related documentation (troubleshooting, specs, etc.)
5. **Communicate:** Note in release notes if user-visible

## Related Documents

- [troubleshooting.md](../troubleshooting.md) — Known issues from ambiguities
- [theme-descriptor.md](../api-reference/theme-descriptor.md) — Documented assumptions
- [asset-specifications.md](../api-reference/asset-specifications.md) — Asset requirements
- [security.md](../security.md) — Security considerations