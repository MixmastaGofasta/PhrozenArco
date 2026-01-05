# Phrozen Arco Bed Layout Reference

## Overview Diagram

```
Y (mm)
  ▲
   │░░░░░░░░│                                                  │░░░░░░░░│
   │░░░░░░░░│           S E R V I C E   A R E A                │░░░░░░░░│
   │░KEEPOUT│                                                  │KEEPOUT░│
322│░(X<50)░│       CHUTE      ════WIPER════                   │(X>250)░│
   │░░░░░░░░│       X=123     X=140────────200                 │░░░░░░░░│
310│░░░░░░░░│─ ─ ─ ─ ─ ─ ─ ─ transit level ─ ─ ─ ─ ─ ─ ─ ─ ─ ─ │░░░░░░░░│
   │░░░░░░░░│                                                  │░░░░░░░░│
300╞════════╧══════════════════════════════════════════════════╧════════╡────┐
   │                                                                    │    │
   │                                                                    │CUT │
260│                                                                    │TER │
   │                         P R I N T   B E D                          │313-│
   │                                                                    │320 │
   │                            300 x 300                               │    │
   │                                                                    │    │
  0└────────────────────────────────────────────────────────────────────┴────┘──▶ X
   0        50                                                250      300  330
            │◄─────────────── SAFE  CORRIDOR ────────────────►│
                           (required when Y > 300)
```

## Service Area Detail (Y > 300)

```
   0      50       123 138 140              200             250      300
   │      │          │   │ │                 │               │        │
   │░░░░░░│          │   │ │                 │               │░░░░░░░░│
   │░░░░░░│          ▼   │ │                 │               │░░░░░░░░│
   │░░░░░░│       CHUTE  │ ├─────────────────┤               │░░░░░░░░│ Y=322
   │░░░░░░│       (purge)│ │  WIPER BRUSH    │               │░░░░░░░░│
   │░░░░░░│              │ │                 │               │░░░░░░░░│ Y=310
   │░░░░░░│            entry                 │               │░░░░░░░░│ (transit)
   │░░░░░░│                                  │               │░░░░░░░░│
───┴░░░░░░┴──────────────────────────────────┴───────────────┴░░░░░░░░┴── Y=300
   │      │                                                  │        │
   │KEEP  │◄──────────────  SAFE CORRIDOR  ─────────────────►│  KEEP  │
   │OUT   │            (must be here before Y > 300)         │  OUT   │
```

## Key Coordinates

**Print Bed**: X = 0-300, Y = 0-300, Z = 0-303

**Service Area** (Y > 300):
- Chute (purge):  X = 123
- Chute entry:    X = 138 (safe approach, left of wiper)
- Wiper brush:    X = 140 to 200
- Service level:  Y = 322

**Cutter** (right of bed at Y = 260):
- Approach: X = 300
- Pre-cut:  X = 313
- Cut:      X = 320

**Keepout Zones** (collision hazard when Y > 300):
- Left rear:  X < 50
- Right rear: X > 250

**Safe Corridor**: X = 50-250 (required when Y > 300)

**Transit Level**: Y = 310 (our override; stock = 280)

## GLOBAL_PARAM Variables

| Variable | Value | Description |
|----------|-------|-------------|
| g_bottom_y | 322 | Service level Y |
| g_bottom_print_y | 280 | Transit Y (we override to 310) |
| g_spitting_start_x | 123 | Chute/purge X |
| g_wipemouth_start_x | 140 | Wiper left edge |
| g_waittingarea_x | 149 | Waiting area X |
| g_wait_pause_x | 169 | Pause position X |

## Waiting Area Macros

| Macro | Ends At | Use For |
|-------|---------|---------|
| PRZ_WAITINGAREA | X=138, Y=322 | Safe transit to service area |
| PRZ_CUT_WAITINGAREA | X=123, Y=322 | Purging, pre-cut heating |
| PRZ_PAUSE_WAITINGAREA | X=138, Y=322 | Pause during filament change |

**PRZ_WAITINGAREA path**:
1. If at Y > 300 and X outside 50-250 → drop to Y=260 first
2. Move X into safe corridor (50-250)
3. Rise to transit level (Y=310)
4. Move X to chute entry (138)
5. Rise to service level (Y=322)
