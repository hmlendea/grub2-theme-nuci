# Concurrency and Scheduling — GRUB2 Theme Nuci

Analysis of concurrency and scheduling aspects. This theme is a static asset package with no runtime execution, no threads, no processes, and no scheduling.

## Concurrency Model

**None.** The theme contains no executable code. It is consumed by GRUB2 at boot time.

| Aspect | Status |
|--------|--------|
| Threads | N/A |
| Processes | N/A |
| Async operations | N/A |
| Parallelism | N/A |
| Race conditions | N/A |
| Locks/mutexes | N/A |
| Atomic operations | N/A |

## GRUB2 Runtime Context

While the theme itself has no concurrency, GRUB2's runtime context is relevant:

### GRUB2 Execution Model

- **Single-threaded:** GRUB2 runs as a single-threaded bootloader
- **Event loop:** Polls for keyboard input, timer ticks
- **No preemption:** Cooperative multitasking only
- **No interrupts:** Firmware handles hardware interrupts; GRUB2 polls

### Theme-Related Concurrency in GRUB2

| Component | Concurrency Aspect |
|-----------|-------------------|
| Asset loading | Sequential during theme init |
| Rendering | Single-threaded frame composition |
| Input handling | Polled in main loop |
| Timeout timer | 1 Hz tick in main loop |
| Video mode change | Synchronous, blocks input |

### No Theme-Level Concerns

The theme does not:
- Spawn threads or processes
- Use callbacks that could race
- Share mutable state across invocations
- Require synchronisation primitives
- Have background tasks

## Scheduling

**Not applicable.** No tasks, jobs, or work items to schedule.

| Scheduling Concept | Applicability |
|-------------------|---------------|
| Task queues | N/A |
| Priority levels | N/A |
| Deadlines | N/A |
| Preemption | N/A |
| Time slicing | N/A |
| Batch processing | N/A |

## Resource Contention

**None at theme level.** GRUB2 manages video memory and framebuffer exclusively during boot.

## Related Documents

- [boot-sequence.md](../behaviour/boot-sequence.md) — GRUB2 boot flow
- [rendering.md](../behaviour/rendering.md) — Rendering pipeline (single-threaded)
- [input-handling.md](../behaviour/input-handling.md) — Input polling loop