# State and Persistence — GRUB2 Theme Nuci

Documents state management and persistence. The theme is a static asset package with no runtime state.

## Theme State

**No runtime state.** The theme contains no mutable state, no configuration that changes at runtime, no user preferences stored.

| State Type | Exists | Description |
|------------|--------|-------------|
| User preferences | No | Theme is static |
| Session state | No | No sessions |
| Configuration state | No | theme.txt is read-only at boot |
| Cache | No | No caching |
| Persistent data | No | No data written |

## GRUB2 Runtime State (Context)

While the theme has no state, GRUB2 maintains state during boot:

### Menu State

| State | Lifetime | Persisted |
|-------|----------|-----------|
| Selected item index | Boot session | No (unless `GRUB_SAVEDEFAULT`) |
| Timeout counter | Boot session | No |
| Submenu depth | Boot session | No |
| Edit buffer | Command session | No |

### Saved Default (Optional)

If `GRUB_SAVEDEFAULT=true` in `/etc/default/grub`:
- Last selected entry saved to GRUB environment block
- Stored in `/boot/grub/grubenv` (or EFI variable on UEFI)
- Persists across reboots
- **Not theme-specific** — GRUB2 core feature

### Environment Block

```bash
# View saved state
grub-editenv /boot/grub/grubenv list

# Typical output:
# saved_entry=1
# prev_saved_entry=0
```

## Theme Asset Persistence

### Filesystem Persistence

Theme assets persist as files:
- `/usr/share/grub/themes/Nuci/theme.txt`
- `/usr/share/grub/themes/Nuci/background.png`
- `/usr/share/grub/themes/Nuci/select_*.png` (8)
- `/usr/share/grub/themes/Nuci/terminal_box_*.png` (9)
- `/usr/share/grub/themes/Nuci/icons/*.png`

**Lifecycle:**
1. Installed by package manager or manual copy
2. Persist until explicitly removed/updated
3. Survive reboots, upgrades (unless package updates theme)

### GRUB2 Config Persistence

`GRUB_THEME` setting persists in:
- `/etc/default/grub` (source of truth)
- `/boot/grub/grub.cfg` (generated, overwritten on `grub-mkconfig`)

**Update Cycle:**
```
Edit /etc/default/grub
    ↓
sudo grub-mkconfig -o /boot/grub/grub.cfg
    ↓
Reboot → new theme active
```

## No Theme-Level Persistence Mechanisms

The theme does not use:
- Databases
- Configuration files (other than theme.txt)
- Registry/preferences systems
- Cloud sync
- Local storage APIs

## State Transition Diagram

```
┌─────────────────┐
│  Theme Installed │  (files on disk)
└────────┬────────┘
         │ grub-mkconfig
         ▼
┌─────────────────┐
│ GRUB_THEME Set  │  (/etc/default/grub)
└────────┬────────┘
         │ Reboot
         ▼
┌─────────────────┐
│ GRUB2 Loads     │  (reads theme.txt, assets)
│ Theme at Boot   │
└────────┬────────┘
         │ User interaction
         ▼
┌─────────────────┐
│ Menu Navigation │  (transient selection state)
└────────┬────────┘
         │ Enter / Timeout
         ▼
┌─────────────────┐
│ Boot Kernel     │  (theme state discarded)
└─────────────────┘
```

## Configuration Drift Detection

### Detect Manual Modifications

```bash
# Check if installed theme matches repository
diff -r Nuci/ /usr/share/grub/themes/Nuci/

# Check GRUB_THEME matches
grep GRUB_THEME /etc/default/grub
```

### Detect Config Drift

```bash
# Verify grub.cfg matches /etc/default/grub
grep GRUB_THEME /boot/grub/grub.cfg
# Should match /etc/default/grub
```

## Backup and Restore

### Backup Theme

```bash
# Backup installed theme
sudo cp -r /usr/share/grub/themes/Nuci /root/nuci-theme-backup-$(date +%Y%m%d)

# Backup GRUB config
sudo cp /etc/default/grub /root/grub-default-backup-$(date +%Y%m%d)
```

### Restore Theme

```bash
# Restore theme files
sudo cp -r /root/nuci-theme-backup-YYYYMMDD /usr/share/grub/themes/Nuci

# Restore GRUB config
sudo cp /root/grub-default-backup-YYYYMMDD /etc/default/grub
sudo grub-mkconfig -o /boot/grub/grub.cfg
```

## Related Documents

- [build-and-deployment.md](../build-and-deployment.md) — Installation/removal
- [configuration.md](../configuration.md) — GRUB_THEME persistence
- [change-guide.md](../change-guide.md) — Rollback procedures
- [troubleshooting.md](../troubleshooting.md) — State-related issues