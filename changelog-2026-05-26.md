# Changelog — 2026-05-26 evening session

Token system: **v3.2** · Figma file: `fysBbW10LRJUNgisaBKXJo`

## 1. Header active-state contrast fix (17 brands)

`Modes/color/header/active-selected` was producing 1.0–2.2:1 against the rendered header background on 17 of 22 brand modes — including 9 cases of pure same-on-same invisibility (active === surface). Post-fix all 22 brands clear WCAG AA 3:1; 16 clear 4.5:1.

Rebound `color/header/active-selected` to a brand-tonal stop per brand luminance class:

| Brand | New binding | New contrast |
|---|---|---|
| Default | `primary/100` | 5.09 |
| Lyllo | `primary/900` | 3.81 |
| Mobilebet | `primary/900` | 5.89 |
| Comeon | `primary/900` | 7.55 |
| Casinostuen | `primary/200` | 4.95 |
| Casinostugan | `primary/200` | 4.95 |
| CherryCasino | `primary/200` | 4.97 |
| GetLucky 2 | `primary/900` | 5.23 |
| Hajper | `primary/900` | 7.52 |
| Sunmaker | `primary/300` | 6.63 |
| Euroslots | `primary/300` | 5.98 |
| Mobilespin | `primary/700` | 8.21 |
| 888 Casino | `primary/800` | 4.79 |
| Galaksino | `primary/200` | 5.59 |
| Snabbare | `primary/900` | 5.81 |
| Get Lucky | `primary/900` | 6.05 |
| Pzbuk | `primary/100` | 5.74 |

**Convention:** for coloured headers, alias active-selected to a brand-tonal stop on the *opposite* luminance side of the header BG. Dark header → lighter stop (`primary/100`–`300`); bright header → darker stop (`primary/700`–`900`); mid → whichever direction has more ramp headroom. Avoid the generic `neutral/900` fallback on coloured headers.

## 2. Hajper neutral ramp respread (surface elevation in Dark)

Under `Theme=Dark`, page/raised/elevated surfaces all resolved to near-identical near-black on Hajper (page→raised contrast 1.04). The deep end of Hajper's neutral ramp was crushed.

| Stop | Old hex | Old L | New hex | New L |
|---|---|---|---|---|
| neutral/800 | `#0f1842` | 0.011 | `#1e2b52` | 0.030 |
| neutral/900 | `#060c27` | 0.004 | `#121d44` | 0.015 |
| neutral/950 | `#030715` | 0.002 | `#070d22` | 0.005 |

Post-fix: page→raised 1.18, raised→elevated 1.18, elevated→700 1.48. Cool-navy character preserved.

## 3. Tertiary ramp enrichment (6 brands)

`tertiary` is consumed by 6 Semantic/Theme tokens — `surface/raised-light`, `surface/elevated-2`, and the `*/purple` accent family (text/icon/surface). Brands with `tertiary` mirroring `primary` rendered those Semantic tokens in the same color as primary, defeating their accent purpose.

Replaced tertiary 50–900 ramps for 6 cool brands using the HSL anchor-aware ladder. Each anchor (500-stop):

| Brand | Old tertiary/500 | New tertiary/500 | Accent direction |
|---|---|---|---|
| Default | `#0052cc` (= primary blue) | `#f59e0b` | amber |
| Snabbare | `#13d46a` (≈ primary green) | `#d946ef` | magenta |
| Mobilebet | `#499b02` (≈ primary lime) | `#7c3aed` | purple |
| Comeon | `#14f34e` (≈ primary neon green) | `#fb7185` | coral |
| Hajper | `#78f2ac` (≈ primary neon green) | `#06b6d4` | cyan |
| Suomikasino | `#333a2d` (near-neutral olive) | `#facc15` | gold |

50/100/200/300 use absolute L=97/92/84/73 with reduced saturation (low-tint paths for `surface/raised-light`). 600/700/800/900 generated via `L - downSpan × {0.15, 0.40, 0.65, 0.85}` where `downSpan = max(8, anchorL × 0.85)`.

## Verification

- Contrast audit: all 22 brands ≥ 3:1 header active vs surface; 16/22 ≥ 4.5:1
- Dark-mode token audit: 108/108 fg/bg pairs ≥ 4.5:1 across the 12 new brands
- Hajper Dark hierarchy verified post-fix
- 12 dark-mode visual screenshots reviewed; no visual regressions
