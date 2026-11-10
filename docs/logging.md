# Logging — GRUB2 Theme Nuci

Documents logging behaviour. The theme itself has no logging capability.

## Theme Logging

**None.** The theme is a static asset package with no runtime execution, no logging framework, and no log output.

## GRUB2 Logging

GRUB2 provides limited logging/debugging:

### Debug Output

Enable with `GRUB_TERMINAL_OUTPUT=console` in `/etc/default/grub`:

```bash
GRUB_TERMINAL_OUTPUT=console
```

Then `grub-mkconfig` and reboot. Boot messages shown on screen.

### GRUB2 Debug Commands

At GRUB2 command line (`c` key):

```bash
# Show debug info
set debug=all

# Show theme path
echo $GRUB_THEME

# Show video info
videomode

# List files
ls /usr/share/grub/themes/Nuci/
```

### Serial Console Logging

For headless systems:

```bash
# In /etc/default/grub
GRUB_TERMINAL=serial
GRUB_SERIAL_COMMAND="serial --speed=115200 --unit=0 --word=8 --parity=no --stop=1"
```

Then boot messages sent to serial port.

## Theme-Related Debug Information

### What GRUB2 Logs About Themes

- Theme load success/failure (internal, not displayed)
- Asset load success/failure (internal)
- Video mode set
- Menu entry count

### What Is Not Logged

- Individual asset decode details
- Rendering performance
- Input event processing
- Timeout tick events

## Debugging Theme Issues

### Visual Inspection

Primary debugging method: observe boot screen for:
- Missing background
- Missing selection highlight
- Missing icons
- Text rendering issues
- Layout problems

### Verification Scripts

Use pre-boot verification:
- `verify-assets.sh` (asset integrity)
- `verify-contracts.sh` (contract compliance)

### GRUB2 Source Debugging

For deep issues, consult GRUB2 source:
- `grub-core/normal/theme.c` — Theme engine
- `grub-core/normal/menu.c` — Menu rendering
- `grub-core/fs/png.c` — PNG decoder
- `grub-core/video/` — Video subsystem

## No Log Files

| Location | Exists |
|----------|--------|
| `/var/log/` | No |
| `/tmp/` | No |
| Journal | No |
| Theme directory | No |

## Related Documents

- [troubleshooting.md](../troubleshooting.md) — Issue diagnosis
- [error-handling.md](../error-handling.md) — Error patterns
- [boot-sequence.md](../behaviour/boot-sequence.md) — When to observe
- [testing.md](../testing.md) — Verification procedures