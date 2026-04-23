# Dark-mode chrome primitives — proposal v1

**Date:** 2026-04-23
**Scope:** 8 `color/chrome/*` primitives × 3 brands (Lyllo, Snabbare, Mobilespin)
**Status:** Draft — no designer specs yet; values are derived suggestions for review.

## Design intent

- Neutral dark greys as the base, with a ~5–8% hue shift toward the brand so the chrome "reads" as brand-aware without being loud.
- Snabbare → cool **blue-grey** (hue ~215).
- Mobilespin → warm **reddish-grey** (hue ~355).
- Lyllo → subtle **pink-grey** (hue ~330). Included for completeness; remove if brand prefers fully neutral dark.
- Foregrounds lean slightly warm/cool to match the bg tint (avoids the "dirty white on tinted bg" effect).
- Elevation ladder: `header-bg < header-surface-1 < header-surface-2` (each step ~6–8 L\* lighter).

## Proposed values

| Token | Lyllo (pink-grey) | Snabbare (blue-grey) | Mobilespin (red-grey) |
|---|---|---|---|
| `color/chrome/header-bg-dark` | `#1a141a` | `#0f1620` | `#1a1214` |
| `color/chrome/header-fg-dark` | `#f2ecf0` | `#e8edf5` | `#f5ebec` |
| `color/chrome/header-surface-1-dark` | `#221a21` | `#161e2a` | `#221618` |
| `color/chrome/header-surface-2-dark` | `#2a2028` | `#1e2736` | `#2a1c1e` |
| `color/chrome/nav-bg-dark` | `#1a141a` | `#0f1620` | `#1a1214` |
| `color/chrome/nav-fg-dark` | `#e5dbe3` | `#d8dde7` | `#e8d8da` |
| `color/chrome/ios-bar-fg-dark` | `#f2ecf0` | `#e8edf5` | `#f5ebec` |
| `color/chrome/menu-row-on-label-dark` | `#968893` | `#7a8599` | `#967a7e` |

## Tint strength reference

If these feel too neutral or too tinted, adjust saturation in HSL:

- **More subtle** (closer to pure grey): drop S by 2–3 points.
- **More brand-present**: push S up by 3–5 points; keep L the same.

Example — Snabbare `header-bg-dark` `#0f1620`:
- HSL ~ `(215°, 36%, 9%)`
- Neutral fallback: `#14171c` (S=11%)
- Bolder: `#0a1830` (S=63%)

## Open questions for brand designers

1. Should brand primary (Lyllo pink, Snabbare blue, Mobilespin red) **desaturate** in dark mode, or stay at full brand saturation? Current Option B keeps brand ramps unchanged.
2. Are there brand-specific dark surfaces we haven't captured (e.g., Mobilespin "dark red card bg")? Would go into the v4 brand extras block.
3. Status colors (success/warning/error) — any dark-mode tuning needed, or let them inherit from the system ramp?

## Next steps once values are approved

1. Add 8 `*-dark` sibling primitives to Collection 1 (brand-moded).
2. Rewire Theme Dark aliases for the 8 tokens from `color/neutral/*` → `color/chrome/*-dark`.
3. Leave the other 23 Theme tokens unchanged.
4. QA: set each brand frame Theme=Dark; hex-diff against any reference designs.
5. Update `project_token_architecture.md` + token reference doc.
